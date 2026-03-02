### SID
- Get SID of users on the system using WMI
```powershell
# wmic.exe
wmic useraccount get name,sid

# Powershell
Get-WmiObject -Class Win32_UserAccount | Select-Object -Property Name,SID | Format-Table
```
### RID
- Relative ID.
- Assigned to new users.
- Represent the user accross the system.
- It is the last bit of SID
- Default RIDs
	- Administrator: 500
	- Normal Users: >1000
- When a users logs in, LSASS process gets it RID from the SAM Registry hive and creates an access token associated with that RID.
