# Interactive Logon
- Login via physical machine or via `runas.exe`
### Local Accounts
The computer checks the NT hash of the password entered by the user to that in the SAM database. NT hash derived from the user is now stored in the `lsass` process.
### Domain Accounts
The computer asks for the TGT from the Domain Controllers, and the TGT gets cached in the system to provide SSO functionality. If the Domain Controller is not available, the computer checks the user credentials in the `Domain Cached Credentails`. To perform interactive logon, the user requires the `SeInteractiveLogonRight`, on the Domain Controllers.

# Network Logon
- When user connects to non-interactive services like SMB, RPC, SQL, etc.
- Requires password, NT Hash, or kerberos keys for authentication. Prone to [PTT](Active%20Directory/Initial%20Foothold/Kerberos#Pass%20the%20Ticket) and [PTH](Active%20Directory/Exploitation/NTLM#Pass%20The%20Hash).
-  Credentials are **NOT** cached in the target machine. Credentials/tickets are only cached in the case of [Kerberos Delegations](Kerberos.md#Kerberos%20Delegations).
```powershell
# Accessing Share
dir \\ws-01\Temp
# Executing Command
psexec.exe \\dc01 cmd.exe
```
- The client negotiate the authentication mechanism using [SPNEGO](Authentication.md#SPNEGO).

# Batch Logon
- Used in context of a scheduled task running as a user. 
```powershell
schtasks.exe /create /tn notepaddaily /tr notepad.exe /sc daily /ru CONTOSO\TaskUser /rp task1234!
```
- The password of the task user is stored in the LSA secret.
- The password is cached in the `lsass` process when the task is executed.

# Service Logon
- Used in context of a service running as a user.
```powershell
sc.exe create MySvc2 binpath= c:\windows\system32\notepad.exe obj=CONTOSO.local\svcUser password=svc1234!
```
- The plain password is stored in the LSA secrets when the task is created. The cached credentials are stored in the `lsass` process when the service is executed.

# NetworkClearText Logon
- Used by Powershell remoting when `CredSSP` is specified. The credentials are sent over an encrypted channel.
- The credential is cached in the `lsass` process in the target machine.
```powershell
New-PSSession -Credential $cred -Authentication Credssp
```

# NewCredential Logon
- Is used when the the user runs `runas` with `/netonly` option.
- The credential are cached in the `lsass` process.
- The credentials are not checked until it is used. 

# Remote Interactive Logon
- Used when a user logins to the computer using RDP.
- Credentials are cached in the `lsass` process.
- It uses [CredSSP](Authentication.md#Cred%20SSP) to send credentials.
- User needs to be part of "Remote Desktop Users" group or "SeRemoteInteractiveLogonRight" to be able to RDP.