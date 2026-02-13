- Users
### User Identification
- `SamAccountName`: Username of the user
- `SID`: domain [SID](Windows/Windows%20Internals/Uncategorized/SID) + [RID](Windows/Windows%20Internals/Uncategorized/SID#RID)
- `DistinguishedName`: used in LDAP to identify objects in an Active Directory. 
### User Secrets
#### NTLM
##### NTHash
```bash
NTHash = MD4(UTF-16-LE(password))
# UTF-16-LE: Little endian format of the UTF-16 charset 
```
- Can be used to perform `Pass-The-Hash` or `Overpass-The-Hash` attacks.
##### LMHash
- Old and discontinued from Vista/Server2008
```bash
padded_password = PAD-14(password.to_upper())
padded_password = password1[0:7] + password2[8:14]
LMHash = DES("KGS!@#$%",password1) + DES("KGS!@#$%",password2)
```
- Dumping password on modern system will show `aad3b435b51404eeaad3b435b51404ee` (LMHash of empty string)
#### Kerberos Keys
- Kerberos keys are derived from the user's password.
##### Algorithms
- AES 256 Key: Used by `AES256-CTS-HMAC-SHA1-96` algorithm (Most Used).
- AES 128 Key: Used by `AES128-CTS-HMAC-SHA1-96` algorithm.
- DES Key: Used by `DES-CBC-MD5` algorithm.
- RC4 Key: NTHash of the user used by `RC4-HMAC` algorithm.
### User Account Control
- A property of a User Class in AD. This property has certain flags:
- `ACCOUNTDISABLE`: Account is disabled and cannot be used.
- `DONT_REQUIRE_PREAUTH`: The account doesn't require Kerberos pre-authentication.
- `NOT_DELEGATED`: This account cannot be delegated through Kerberos delegation.
- `TRUSTED_FOR_DELEGATION`: Kerberos Unconstrained Delegation is enabled for this account and its services. [SeEnableDelegationPrivilege](http://www.harmj0y.net/blog/activedirectory/the-most-dangerous-user-right-you-probably-have-never-heard-of/) required to modify it.
- `TRUSTED_TO_AUTH_FOR_DELEGATION`: The Kerberos S4U2Self extension is enabled for this account and its services. [SeEnableDelegationPrivilege](http://www.harmj0y.net/blog/activedirectory/the-most-dangerous-user-right-you-probably-have-never-heard-of/) required to modify it.
### User Properties
- `Description`
- `AdminCount`
- `MemberOf`: Groups of which the user is a member of
- `PrimaryGroupID`: Primary group of the user. Does not appear in MemberOf.
- `ServicePrincipalName`
- [msDS-AllowedToDelegateTo](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-ada2/86261ca1-154c-41fb-8e5f-c6446e77daaa) -> The list of services for which the user (and its own services) can impersonate clients using Kerberos Constrained Delegation. [SeEnableDelegationPrivilege](http://www.harmj0y.net/blog/activedirectory/the-most-dangerous-user-right-you-probably-have-never-heard-of/) required to modify it.
- [User Account Control](#User%20Account%20Control)

### Computer Accounts
Each computer in the domain has it's own user. Users are stored in the `User` class. Computers are stored in the `Computer` class which a subclass of the `User` class. It is stored as `Hostname$`.

### Trust Account
When trust is established with a domain, a trust user account is created which also ends with a `$` symbol. The username of this account is the net-bios name of the domain.
This user stores the trust key, as the NThash or kerberos keys.

[User Enumeration](../Domain%20Enumeration/Powershell/Users.md)