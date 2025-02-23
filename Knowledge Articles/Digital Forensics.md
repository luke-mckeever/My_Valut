# Digital Forensics
#KA #Forensics

---
## 🔍 Locard's Exchange Principle 🌟

> "Every contact leaves a trace." 🕵️‍♂️

Locard's Exchange Principle, conceptualized by Dr. Edmond Locard, underpins digital forensics by asserting that interactions between systems, devices, or users leave traces. These traces include logs, metadata, digital fingerprints, and artefacts such as deleted files or unauthorized access logs. By meticulously analysing these digital remnants, investigators can link cybercriminals to breaches, uncover unauthorized activities, and reconstruct events. This principle reinforces the importance of detailed data collection and analysis, ensuring accurate attribution and robust evidence in digital investigations.

![locard's exchange principle](http://www.c-jump.com/bcc/t155t/Week03a/const_images/002_Locard.png)

---

## ⬇Order of Volatility

```mermaid
graph TD
	A[CPU Registers & Cache] --> B[RAM & Active Netowork Connections]
	B --> C[Temp Files & System Logs]
	C --> D[Disk Storage Data]
	D --> E[Remote Logging]
	E --> F[Archive Media]
```

#### 1. CPU Registers & Cache
- Process Registers
- Cache Memory
- Running Processes
#### 2. RAM & Active Network Connections
- Random Access Memory (Session data)
- Network Connections (Ports/Connections)
- Routing Table
- ARP Cache (Maps IP addresses to MAC addresses)
#### 3. Temp Files & System Logs
- Temporary Files (Temp Directories)
- Swap Files 
- System and Application Logs
#### 4. Disk Storage Data
- Hard Disk Drives
- Solid State Drives
- System Configurations
#### 5. Remote Logging
- Endpoint Telemetry
- Centralized Log Collection
#### 6. Archive Media
- Long-term Storage Media
- Backups 
- Security Controls (C.I.A.)

---
## Chain of Custody
Tracks evidence through the entirety of an investigation. 
Provides integrity of evidence 

---
## 🔍 Windows Registry Keys - The Hidden Treasure Chest - [[Registry Explorer]]🎩
Windows Registry is a hierarchical database used by Windows to store configuration settings, user preferences, and system information. It can be leveraged for forensic analysis, security investigations, and malware detection.

### 🏠 User Activity Traces
Windows maintains a variety of logs within the registry that record user activity. These keys store historical information that can reveal what actions a user has performed on the system.

- `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths`
  - This key stores the paths manually entered into Windows Explorer’s address bar, making it useful for tracking user navigation patterns.
- `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`
  - Stores a list of recently accessed documents, including file names and extensions. This data can reveal what files a user interacted with.
- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall`
  - Maintains records of all installed software, including timestamps and version numbers. This information is useful for detecting unauthorized software installations or malware persistence.


### 🔥 Persistence Mechanisms (Malware Hiding Spots)
Threat actors frequently use the registry to establish persistence, ensuring that their malicious payloads execute upon system startup.

- `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run`
  - Programs listed under this key automatically launch during startup. Malware often abuses this key to maintain persistence.
- `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce`
  - Executes a program once upon the next system startup. Attackers use this method for executing payloads only once to evade detection.
- `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\DisallowRun`
  - This key can be modified to prevent certain applications from executing, potentially used by administrators for security or by malware to disable security tools.

> 🚨 **Pro Tip:** Use **Regedit.exe** or **PowerShell** to analyze and export these registry keys for forensic examination.

---

## Important Forensic Artifacts 🎁

#### config Files 🗃️
**Folder location**: "*C:Windows/System32/config*"

#### %SYSTEMROOT%\System32\Config
|  **File Name** |    **Registry Key**     |  **Description**  |
|----------------|-------------------------|-------------------|
| DEFAULT | HKEY_USERS\.DEFAULT | A registry hive that stores default user profile settings. Useful for determining the system's initial configuration and default behavior before user-specific modifications. |
| SAM (Security Account Manager) | HKEY_LOCAL_MACHINE\SAM | Stores user account and credential information, such as hashed passwords. It is critical for analyzing user accounts, unauthorized access, or privilege escalation attempts. |
| SECURITY | HKEY_LOCAL_MACHINE\SECURITY | Contains system security policies and local security authority settings. Forensic investigators use it to analyze access controls, audit policies, and potential tampering with security configurations. |
| SOFTWARE | HKEY_LOCAL_MACHINE\SOFTWARE | A registry hive containing information about installed software, settings, and system policies. It helps identify installed programs, user preferences, and potentially malicious software. |
| SYSTEM | HKEY_LOCAL_MACHINE\SYSTEM | Stores system-wide configuration data, including services, drivers, and hardware profiles. Forensic investigators analyze it to detect changes to system configurations, malicious drivers, or service persistence mechanisms. |


#### %USERPROFILE%
|  **File Name** |    **Registry Key**     |  **Description**  |
|----------------|-------------------------|-------------------|
| NTUSER.DAT | HKEY_CURRENT_USER | user-specific Windows Registry file that contains configuration settings such as desktop setup and application preferences. It is important in digital forensics for analyzing a user's activities on a computer. |
| USRCLASS.DAT | HKEY_CURRENT_USER\Software\Classes | user-specific Windows Registry file containing settings for software and COM objects associated with a user profile. It is useful in digital forensics for analyzing user behavior and software usage. |

---

## 🏷️ Shell Bags - Windows Explorer’s Memory Bank - [[Shell Bags Explorer]]🧠
Shell Bags are forensic artifacts that store metadata about folders viewed in Windows Explorer. They help track a user’s interaction with the file system, even if the original folders were deleted.

### Where Are Shell Bags Stored?
Windows stores Shell Bags in the registry under the following keys:

- `HKEY_USERS\{User SID}\Software\Microsoft\Windows\Shell\Bags`
  - Contains folder customization settings such as layout preferences (list view, icon view, etc.).
- `HKEY_USERS\{User SID}\Software\Microsoft\Windows\Shell\BagMRU`
  - Maintains a history of folders accessed by the user, even those on external devices and network shares.


### LastVistitedPidlMRU & OpenSavePidlMRU

#### MRU = Most recently used 

`LastVisitedPidlMRU` and `OpenSavePidlMRU` are both registry keys in Microsoft Windows that store information related to the user's recent activity in File Explorer.

Registry Location: 
*`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\*`*
Replace `*` with either *`LastVistitedPidlMRU`* Or *`OpenSavePidlMRU`

**LastVisitedPidlMRU**: This registry key tracks the list of recently accessed folders. It stores the item ID lists (PIDLs) of folders that the user has recently visited, helping in populating the recent items or frequent places lists in File Explorer.

**OpenSavePidlMRU**: Similar to LastVisitedPidlMRU, this key stores the PIDLs of recently accessed folders specifically in the context of open and save dialog boxes. It helps in displaying recent folders when a user is opening or saving a file, providing quick access to frequently used locations.


#### Recently Visited Documents

*`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`*

The `RecentDocs` key stores entries for each type of document the user has opened. Each entry typically contains information about the file, such as the filename and the path to its location. The purpose of this key is to help populate the "Recent Documents" list in the Windows user interface, allowing users quick access to the files they have recently worked with.

#### Run Dialog Box Recent Executions

*`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU`*

The `RunMRU` key contains entries that record each command entered into the Run dialog, stored as values with the commands themselves and a corresponding identifier. The key allows Windows to display a history of commands when the user accesses the Run dialog box again, providing quick access to previously executed commands.

#### Recently Typed Paths 

*`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths`*

Each entry in the `TypedPaths` key is typically labeled as `url1`, `url2`, `url3`, etc., and stores the actual path that was typed. By retaining these paths, Windows can offer them as suggestions or autocomplete options when the user begins to type similar paths in the future. This feature improves user efficiency by reducing the amount of typing needed to navigate to commonly used folders or drives.

#### Program Execution

*`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`*

Within this key can provide a list of all executed processes on the system for all time.
This will also show the last time a process was executed and a count of how many times.

In cybersecurity and forensic contexts, the information within the UserAssist key can be critical. Analysts can extract and decode the data to determine which applications were executed, how frequently, and the last time they were run. This is useful in understanding a user’s habits or detecting malicious activity, such as the execution of malware.

The data within the UserAssist entries is encoded (typically using ROT13) to obscure the details from casual observation.

#### Windows Autoruns 

- System Autoruns 
*`Software\Microsoft\Windows\CurrentVersion\Run`*

This registry key configures programs to start automatically when the system boots up, affecting all users of the computer.

- User Autoruns
*`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Run`*

This registry key configures programs to start automatically when a specific user logs in, affecting only that user's session.


#### Windows Start-up Executions

- System Start-ups
*`Software\Microsoft\Windows\CurrentVersion\RunOnce`*

This registry key is used to schedule programs to run once and only once on the next system startup, affecting all users.

- User Start-ups
*`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\RunOnce`*

This registry key is used to schedule programs to run once and only once on the next login of the specific user, affecting only that user's session.

#### Windows Service Configurations

*`SYSTEM\CurrentControlSet\Services`*

This registry key contains configurations for all system services. It includes settings such as the service's start type, path to its executable, and other specific parameters that control how the service operates on the system.

#### Scheduled Tasks

*`SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache`*


This registry key is critical for security as it holds configurations for all scheduled tasks, which can be targeted by attackers to execute malicious activities automatically. Ensuring the integrity and monitoring changes to this key can help prevent unauthorized tasks from compromising system security.


### LNK Files / Windows Shortcut Files

[Forensics Wiki](https://forensics.wiki/lnk/)

Link File Location - 
*`C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent Items`*

The Windows Shortcut file has the extension .lnk. It basically is a metadata file, specific for the Microsoft Windows platform and is interpreted by the Windows Shell. The file format indicates that these files contain a specific signature, 0x4C (4C 00 00 00) at offset 0 within the file/stream. Further, the GUID (CLSID) 00021401-0000-0000-c000-000000000046 stored at byte offset 4 makes a good identifier.

Understanding this file format can be extremely useful for an analyst, as not only are shortcut files employed from (at least) Windows 95 through Windows 10, but the binary format is also used in the numbered streams within *.automaticDestinations-ms and *.customDestinations-ms [Jump Lists](https://forensics.wiki/jump_lists/) files on [Windows 7](https://forensics.wiki/windows_7/) and later.


### Prefetch Files - [[Prefetch Parser]]

[Foresnsics Wiki](https://forensics.wiki/prefetch/)

Prefetch File Location - 
*`C:\Windows\Prefetch`*

Windows Prefetch files, introduced in [Windows XP](https://forensics.wiki/windows/), are designed to speed up the application startup process. Prefetch files contain the name of the executable, a Unicode list of DLLs used by that executable, a count of how many times the executable has been run, and a timestamp indicating the last time the program was run. Although Prefetch is present in Windows 2003, by default it is only enabled for boot prefetching. The feature is also found in [Windows Vista](https://forensics.wiki/windows/), where it has been augmented with [SuperFetch](https://forensics.wiki/superfetch/), [ReadyBoot](https://forensics.wiki/readyboot/), and [ReadyBoost](https://forensics.wiki/readyboost/).

### Jump Lists - [[JumpList Explorer]]

[Forensics Wiki](https://forensics.wiki/jump_lists/)

Jump List File Location - 
*`C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent`*

A **Jump List** is a feature in Windows that provides quick access to recently opened files, frequently used tasks, or pinned items for a specific application. It appears when you right-click an app icon in the **taskbar** or **Start menu**, helping users navigate their workflow efficiently.

![[Pasted image 20250214183144.png]]

After browsing to the above file path the two directories containing these files are not shown by default executing `dir /ad` will show you the two hidden directories:

- AutomaticDestinations - Generated by windows 
- CustomDestinations - Custom Jump Lists

viewing the contents of either directory will provide the jump list files with the extension
`<directory>-ms`

below is a list of some example Application ID's to assist in identifying the jump file

e.g -> `12dc1ea8e34b5a6.automaticDestinations-ms` - Microsoft Paint

[Jump List App ID's](https://github.com/EricZimmerman/JumpList/blob/master/JumpList/Resources/AppIDs.txt)


