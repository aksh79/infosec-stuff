- Particular view of the Operation System.
- Used to have 3 subsystems
	- Windows
	- Posix (dropped by 8.1)
	- OS/2 (dropped by XP)
- Currently only supports Windows Subsystem.
---
# Windows Subsystem
- `csrss.exe` - windows subsystem process.
- Owns keyboard and mouse.
- API functions uses ALPC facility to notify `csrss.exe` about events. (eg. process creation)
## Windows Subsystem API
### [Win32 API](Win32%20API.md)
- Also called Win32 API
- Classic C APIs from the initial day of windows NT.
- Some APIs are COM (Component Object Model)
### .NET
- Managed libraries running on top of the CLR
- Common language: C#, VB.NET, F#, C++, CLI, Powershell
### Windows Runtime (WinRT)
- New unmanaged API available from Windows 8.
- Built on top of enhanced version of COM.
--- 
# Subsystem DLLs
- Every executable belongs to one subsystem.
	- This information is stored in the PE image.
- Executables belonging to a subsystem calls API exposed through subsystem DLLS
- Windows subsystem have the following subsystem DLLs (which calls into native dll (NTDLL.dll))
	- kernel32.dll
	- user32.dll
	- advapi32.dll
- Few images have no subsystem - calls native dll.
---
# Native DLLs
- This is what `NTDLL.dll` implements and all subsystem APIs calls into this dll.
- Lowest user mode DLL.
- Undocumented.
- 