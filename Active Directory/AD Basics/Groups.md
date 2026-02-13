- Allows members of the groups to certain resource.
- Also has `SamAccountName` and `SID`.
### Important Groups
- `Administrators` : Full access to the domain.
- `Domain Admins` : member of Administrators. In all forest
- `Enterprise Admins` : member of Administrators. Only exists in root domain in a tree.
- `DNSAdmins`: DNS Administrators Group. Can execute arbitrary code in domain controller using DLLs
- `Protected Users`: enforce more security than normal users:
	- Only Kerberos.
	- Deny DES and RC4 encryption.
	- No delegation (constrained & unconstrained).
	- Cannot review TGTs beyond limit.
- `Schema Admins`: Can modify AD Schema
- `Account Operator`: Can modify member of groups in the domain, excluding administrator group.
- `Backup Operators`: Can perform backup and restore files in the domain controllers. Can also modify files in domain controller
- `Print Operators`: Log into domain controllers.
- `Server Operators`: Can log into domain controller and manage configs.
- `Remote Desktop Users`: Can login to domain controller through RDP.
- `Group Policy Creator Owner`: Can edit GPOs in the domain.

### Group Scope
- `Universal Groups`: Can have members from the same forest and grant permission in the same forest or trusted forest. Eg: `Enterpise Admins`.
- `Global Group`: Can have member from the same domain and grants permission in domain of the same forest or trusting domains. Eg: `Domain Admins`
- `Domain Local Groups`:  Can have member from the same domain, but grans permission only in their domain. Eg: Administrator group.

[Group Enumeration](../Domain%20Enumeration/Powershell/Groups.md)