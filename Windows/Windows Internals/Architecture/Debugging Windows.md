# Debuggers
- 4 major based on `DbgEng.dll`:
	- Cdb - user mode, console based
	- Ntsd - user mode, consoe based
	- Kd - kernel mode, console based
	- WinDbg -  user/kernel mode, UI based.
---
# WinDbg
- Regular commands - works on program being debugged.
- Meta commands - works on debugger.
- Extension commands - starts with !
## User Mode Debugging
- As simple as running a new process or attaching to a running process.
## Kernel Mode Debugging
- Local Kernel Mode Debugging.
	- Debugging the current machine.
	- Limited debug functionality - cannot set breakpoints.
- Remote Kernel Mode Debugging.
	- Need to have two windows instance - debugger & debugee. 