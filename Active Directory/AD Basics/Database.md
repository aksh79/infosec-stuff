- The database is where all the Active Directory Objects are stored.
- In Windows Active Directory, the database is stored at `C:\Windows\NTDS\ntds.dit` in the domain controller.
# Classes
- Schema that defines `ObjectClasses` like:
	- User Class
	- Group Class
	- Computer Class
- All classes in Active Directory is a subclass of the `Top` Class, that has properties like `ObjectClass` and `ObjectGUID`.
	- `ObjectClass`: Lists the classes of an object. Current & Parent
	- `ObjectGUID`: Object Global Unique Identifier.
> classes can be attached to `auxiliary` classes. These are not visible in the ObjectClass
> Eg: User & Group class are attached to the `Security-Principal` auxiliary class which defines properties like `SamAccountName` and `SID`

# Properties
- Each class have several properties such as:
	- SID
	- Name
	- UserAccountControl
- These properties are readable by everyone except few like:
	- Password
	- Confidential Properties.
- The password property can only be written, not read.
- Confidential properties are read by only authorized users. These are marked by setting the 128 flag in `searchFlags`  of the property definition.
- Some properties needs to be validated before being written to the object. This is achieved using `Validated Writes`.
## SID
- This property is used to identify principals.
1. `DomainSID`: This is used to identify a domain
```powershell
PS C:\Users\Administrator> Get-ADDomain | select domainSID

domainSID
---------
S-1-5-21-2968811138-1285828788-388312258   
```
2. `PrincipalSID` Used to Identify principals. Usually `[DomainSID]-[RID]`.
```powershell
PS C:\Users\Administrator> Get-ADUser -Filter * -Properties * | select SamAccountName,SID

SamAccountName SID
-------------- ---
Administrator  S-1-5-21-2968811138-1285828788-388312258-500
Guest          S-1-5-21-2968811138-1285828788-388312258-501
krbtgt         S-1-5-21-2968811138-1285828788-388312258-502
user2          S-1-5-21-2968811138-1285828788-388312258-1103
user1          S-1-5-21-2968811138-1285828788-388312258-1107    
```
- Common SIDs for buildin objects:
	- `Administrator` : S-1-5-21-domainSID-500
	- `Domain Admins` : S-1-5-21-domainSID-512
	- `Domain Users` : S-1-5-21-domainSID-513
	- `Enterprise Admins` : S-1-5-21-domainSID-519
- There are some SID for special situations:
	- `Authenticated Users` : S-1-5-11
	- `Principal Self` : S-1-5-10
## Distinguished Names
- Used to identify objects in AD, using paths/hierarchy of the object
```bash
CN=Administrator,CN=Users,DC=doctor,DC=local
CN=Administrator,OU=Users,DC=doctor,DC=local
```
1. `Domain Components (DC)` : Identifies the domain. DC for `it.doctor.local` would be `DC=it,DC=doctor,DC=local`.
2. `Organizational Unit (OU)` : Identifies organisational units used to store objects.
3. `Common Name (CN)` : Identifies objects or containers.

# Partitions
Active Directory database has the following partition
- `Domain` : Stores domain objects such as users, groups, computer, GPOs.
- `Configuration` : Stores configuration data for the domain, such as well known objects SIDs.
- `Schema` : Stores definition of the classes and objects.
- `DomainDnsZones` : Stores DNS records.
- `ForestDnsZones` : Stores DNS records for the forests.
```powershell
PS AD:\> ls

Name                 ObjectClass          DistinguishedName
----                 -----------          -----------------
doctor               domainDNS            DC=doctor,DC=local
Configuration        configuration        CN=Configuration,DC=doctor,DC=local
Schema               dMD                  CN=Schema,CN=Configuration,DC=doctor,DC=local
DomainDnsZones       domainDNS            DC=DomainDnsZones,DC=doctor,DC=local
ForestDnsZones       domainDNS            DC=ForestDnsZones,DC=doctor,DC=local      
```

# Querying Database
### LDAP
- Lightweight Directory Access Protocol
- Can retried each and every Active Directory object.
- Access all the domain data using ports:
	- TCP: 380 - LDAP
	- TCP/SSL: 636 - LDAPS
- LDAP can be queried using `ldapsearch` utility
```bash
ldapsearch -H ldap:\\<target> -x -LLL -W -D "username@domain" -b "searchbase" "filter" "attribute"
# -x : Use simple authentication
# -LLL : print the information in LDIF version
# -W : Prompt for authentication
# -D "username@domain" : Bind with the username
# -b "searchbase" : the base DN where the search will start from
# "filter" : The ldap query to process
# "attribute" : the attribute of the object to fetch
# Examples:

ldapsearch -H ldap://192.168.122.50 -x -LLL -W -D "Administrator@doctor.local" -b "dc=doctor,dc=local" "(ObjectClass=Computer)" "SamAccountname"

dn: CN=DOCTOR-DC,OU=Domain Controllers,DC=doctor,DC=local
sAMAccountName: DOCTOR-DC$

dn: CN=DRWS01,CN=Computers,DC=doctor,DC=local
sAMAccountName: DRWS01$
```
### ADWS
- Active Directory Web Services.
- Protocol to query and manipulate domain objects using SOAP messages.
- Compatible with LDAP filterer.
- Protocol used by Active Directory RSAT module.