#powerview #ldap #mimikatz #powershell 
When a user requests for ST, and if the service owner has the `TRUSTED_FOR_DELEGATION` flag set in [User Account Control](../AD%20Basics/Users.md#User%20Account%20Control), the ST received from the KCD will have the `OK_AS_DELEGATE` flag set.
To set `TRUSTED_FOR_DELEGATION` flag in User Account Control, the user needs to have `SeEnableDelegationPrivilege` privilege.
#### Ticket Acquisition
- Client requests for the ST for a service with SPN `HTTP\webserv` which is owned by `websrv$` machine account.
- The KDC checks for `TRUSTED_FOR_DELEGATION` in `websrv$` account's UAC. KDC returns an ST with `OK_AS_DELEGATE` and `FORWARDABLE` flag set.
- The Client checks of these flags and asks a `FORWARDED` TGT. The KDC responds with a TGT with `FORWARDED` flag set.
- The Client sends the ST and the `FORWARDED` TGT to the service for access. 
- The Serivice uses this TGT to request ST for `MSSQLSvc\dbsrv` serivce.
- Websrv uses the ST to get access to the MSSQL service.
### Flaw
If the service account that is `TRUSTED_FOR_DELEGATION` is compromised, we get access to TGTs for uses that have tried to connect to this service. Mimikatz can be used to get access to the TGTs cached in lsass process.
```mimi
mimikatz sekurlsa::tickets
```
### Identifying accounts with unconstrained delegation
- LDAP query
```ldap
(UserAccountControl:1.2.840.113556.1.4.803:=524288)
```
- Powerview
```powershell
Get-DomainComputer -unconstrained
```
- Active Directory Module
```powershell
Get-ADComputer -Filter {TrustedForDelegation -eq $True}
Get-ADUser -Filter {TrustedForDelegation -eq $True}
```
