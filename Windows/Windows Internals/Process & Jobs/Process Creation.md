# Process creation flow
- Open executable image and verify PE header.
- Create [EPROCESS](Data%20Structures.md).
- Create main thread, which creates ETHREAD.
- Notify CSRSS.
- Loader takes control.
	- Creates [PEB](Data%20Structures.md)
	- Creates TEB
	- Loads and maps DLLs/Modules. (read from PE header)
	- `DllMain` called with [reason](DLLs.md) `DLL_PROCESS_ATTACH`.
- Execute entry point of the function.
# Using Win32 APIs
- Create process using `CreateProcessW()`
```c
#include<stdio.h>
#include<Windows.h>

int main() {
    WCHAR procname[] = L"notepad";
    STARTUPINFO si = { sizeof(si) };
    PROCESS_INFORMATION pi;
    BOOL created = CreateProcessW(
        NULL,
        procname,
        NULL,
        NULL,
        FALSE,
        0,
        NULL,
        NULL,
        &si,
        &pi
    );
  
    if(created) {
        printf("Process Launched!\n");
        printf("PID: %d\n", pi.dwProcessId);
        WaitForSingleObject(pi.hProcess, INFINITE);
        printf("Closing handles!");
        CloseHandle(pi.hProcess);
        CloseHandle(pi.hThread);
    } else {
        printf("Something happened!\n");
        DWORD errcode = GetLastError();
        printf("ErrorCode: %d", errcode);
    }
}
```
