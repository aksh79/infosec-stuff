# LSASS
- The credentials are cached in a process called LSASS (Local Security Authority Subsystem Service), which runs as `lsass.exe`.
- Manages the security related operations of the computer, including user authentication.
- Credentials are cached when a user performs an interactive logon, physically or via RDP.
- LSASS uses the credentials cached by various SSPs like:
	- `Kerberos SSP`: Stores tickets and keys for logged on users.
	- `NTLP SSP`: Stores NTLM hash of the current users.
	- `Digest SSP`: Used to store clear text password in memory before windows 2008 R2. 
- LSASS process is accessible to users with `SeDebugPrivilege` (Administrators hold this privilege)
- Mimikatz's `sekurlsa` module can be used to extract credentials from the LSASS memory.
- LSASS is a very heavily monitored and secure process. MDR and EDRs keeps a big eye on LSASS all the time. Even if you open a handle to the process, EDRs will generate an alert.
# Registry Credentials
### SECURITY Hive
- `HKLM\Security\Policy\Secrets`
- Also knows as the LSA Secrets
- A special storage in registry only accessible to `SYSTEM`.
- Encrypted with SysKey or BootKey
- The LSA Secrets stores:
	- `Domain controller account`: Username and password of the computer accounts are stored here. 
	- `Service User Passwords`: when a service needs to be run by a user on the computer, the password for that user is stored here. 
	- `AutoLogon Password`: If Auto-logon is enabled, the password is stored in the LSA secrets.
	- `DPAPI Master Key`:  The master keys are stored here. If retrieved, user data can be decrypted.
### SAM Hive
SAM hive contains the NT hash of the local users for that machine. 
### SYSTEM Hive
The System Hive contains the SysKey and BootKey that is used to encrypt the data in SAM and SECURITY hives.
# Powershell History
- Passwords can be stored in powershell history files.
- Query powershell history file:
```powershell
Get-PSReadLineOption).HistorySavePath
```
- Get Powershell history for the user:
```powershell
Get-childItem C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadLine\ConsoleHost_history.txt
```

- To Disable history in powershell
```powershell
Set-PSReadLineOption -HistorySaveStype SaveNothing # Disables history in powershell
```