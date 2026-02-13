### smbserver.py
```bash
# runs a rogue smb server without authenication with a "public" share and 
smbserver.py -smb2support <share_name> <share_directory>

# specify username and password
smbserver.py -smb2support -username <username> -password <password> <share_name> <share_directory>
```

### secretsdump.py
```bash
# dump hashes from 
secretsdump.py -sam <sam_dump> -system <system_dump> LOCAL
```
