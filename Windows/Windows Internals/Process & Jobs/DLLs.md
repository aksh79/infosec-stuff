- Dynamic Link Libraries (DLLs)
- Loadable modules, mapped into a process address space
- Contains:
	- Code
	- Data
	- Resource
- Can be shared between processes. Process A can point to a certain location in memory in RAM where the DLL is present, and another process B can also point to the same location in memory to access the same DLL. It will very expensive, to copy the DLL for every process. duh!
- Entry point for a DLL is a function called `DllMain`.
- `DllMain` function takes a reason in it's parameter:
	- `DLL_PROCESS_ATTACH`
	- `DLL_PROCESS_DETACH` - `DllMain` is called with this reason when `ExitProcess` is called by a thread.
	- etc.
---
# Linking Types
### Implicit
- DLLs are mentioned in the PE header/import tables.
### Explicit
- Need to call `LoadLibrary` to link a DLL explicitly at runtime.
- Returns a NULL value if DLL was not found.
- Functions resolved with `GetProcAddress`
--- 
# DLL Search Paths
- Knows DLLs
- Executable directory.
- Directory returned by `GetSystemDirectory` (C:\Windows\System32)
- Directory returned by `GetWindowsDirectory` (C:\Windows)
- Current Directory
- PATH environment variable.