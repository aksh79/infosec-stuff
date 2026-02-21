- Windows NT 4 (4.0)
- Windows 2000 (5.0)
- Windows XP (5.1)
- Windows Server 2003 (5.2)
- Windows Vista, Server 2008 (6.0)
- Windows 7, Server 2008 R2 (6.1)
- Windows 8, Server 2012 (6.2)
- Windows 8.1, Server 2012 R2 (6.3) <- (reported as 6.2)
- Windows 10, Server 2016 (10.1) <- (reported as 6.2)
### Using Windows API to get Windows Version
- `GetVersionEx()` function was used traditionally to get the version information, but is now deprecated.
- New helper functions defined in `versionhelpers.h` is used now:
	- `IsWindowsXPSP3OrGreater()`
	- `IsWindows10OrGreater()`
	- `IsWindowsServer()`
- These functions are implemented with `VerifyVersionInfo()`
- It requires manifest file to get correct information
```c
void winversion() {
    // deprecated but can be enabled with macro
    OSVERSIONINFO vi = { sizeof(vi) };
    GetVersionEx(&vi);
    printf("%u.%u.%u\n", vi.dwMajorVersion, vi.dwMajorVersion, vi.dwBuildNumber);
    BOOL ans;
    if (ans = IsWindows7OrGreater()) {
        printf("7 and greater");
    }
}
```