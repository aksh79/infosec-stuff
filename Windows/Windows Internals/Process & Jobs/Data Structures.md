# User Mode Process Structure
- Process Environment Block (PEB) is the usermode data structures that holds process information
- We can use the `!peb` command to enumerate the PEB block of the debugged process.
```bash
0:007> !peb
PEB at 0000005930b10000
    InheritedAddressSpace:    No
    ReadImageFileExecOptions: No
    BeingDebugged:            Yes
    ImageBaseAddress:         00007ff69cca0000
    NtGlobalFlag:             0
    NtGlobalFlag2:            0
    Ldr                       00007ff9570fc4c0
    Ldr.Initialized:          Yes
    Ldr.InInitializationOrderModuleList: 000001f786cc2510 . 000001f786cfbba0
    Ldr.InLoadOrderModuleList:           000001f786cc2680 . 000001f786cfba50
    Ldr.InMemoryOrderModuleList:         000001f786cc2690 . 000001f786cfba60
                    Base TimeStamp                     Module
            7ff69cca0000 6df9f3ad Jun 20 00:03:09 2028 C:\Windows\system32\notepad.exe
```
# Kernel Mode Process Structure
- `EPROCESS` is the kernel mode data structure that that manages information for all the process. This acts as a high level container.
- `KPROCESS` is the first block in the `EPROCESS` structure which holds more low level data about the process. Eg - Scheduling information, priority etc. Also called and named `Pcb` (Process Control Block).
- All process are linked as a doubly linked list using the `LIST_ENTRY` structure named `ActiveProcessLink`
- `LIST_ENTRY` contains conains pointer to next `LIST_ENTRY` and previous `LIST_ENTRY`.
- We can use the debugger to look at the `_EPROCESS` structure:
```bash
lkd> dt nt!_eprocess
   +0x000 Pcb              : _KPROCESS
   +0x438 ProcessLock      : _EX_PUSH_LOCK
   +0x440 UniqueProcessId  : Ptr64 Void
   +0x448 ActiveProcessLinks : _LIST_ENTRY
   ...
```
- We can use use the `!process 0 0 <processname>` to get the address of the `EPROCESS` structure.
```bash
lkd> !process 0 0 explorer.exe
PROCESS ffffd98156c89080 # Address of the EPROCESS
    SessionId: 1  Cid: 1518    Peb: 00fb5000  ParentCid: 16d0
    DirBase: a0a56000  ObjectTable: ffff99082488fe00  HandleCount: 2280.
    Image: explorer.exe
```
- We can use this address and pass it to the `dt` command to get actual values of the `EPROCESS` instance.
```bash
lkd> dt nt!_EPROCESS ffffd98156c89080
   +0x000 Pcb              : _KPROCESS
   +0x438 ProcessLock      : _EX_PUSH_LOCK
   +0x440 UniqueProcessId  : 0x00000000`00001518 Void
   +0x448 ActiveProcessLinks : _LIST_ENTRY [ 0xffffd981`56e524c8 - 0xffffd981`56c83748 ]
   +0x458 RundownProtect   : _EX_RUNDOWN_REF
   +0x460 Flags2           : 0xd000
   +0x460 JobNotReallyActive : 0y0
   +0x460 AccountingFolded : 0y0
   +0x460 NewProcessReported : 0y0
   +0x460 ExitProcessReported : 0y0
```
