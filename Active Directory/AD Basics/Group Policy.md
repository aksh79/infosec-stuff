- Are set of policies/rules/action that apply to AD [Users](Users.md) and [Computers](Computers.md).
# GPOs
- Group policy objects.
- Each GPO has a set of policies.
- GPOs can be applied to the machines or just the user's session.
##### GPO scope
- GPOs are attached to one of the following containers:
	- Domain
	- Organisational Units
	- Site
- GPO precedence:
	1. Organisational Unit
	2. Domain
	3. Site
	4. Local Policies
- Local Policies have the lowest preference, while OUs have the highest preference.
- AD GPOs can specifiy itself with "No Override", which will have the highest preference, even above OUs.
- GPOs can have [WMI](../../Windows/Windows%20Internals/Uncategorized/WMI.md) query associated will allows it to filter the computer to which the GPOs is being applied to.
- Group Policies are update every 90 minutes by every system except the domain controller. DCs update every 5 minutes.
- GUIDs are used to identify GPOs.
# Group Policy Template
- Directories stored in `//<domain>/SYSVOL/<domain>/Policies` share folder.
- Each folder is named as the GUID of the GPO.
- Each directory contains:
	- **Adm**: Contains all the .adm files for this Group Policy template.
	- **Scripts**: Contains all the scripts and related files for this Group Policy template.
	- **User**: Includes a Registry.pol file that contains the registry settings that are to be applied to users. When a user logs on to a computer, this Registry.pol file is downloaded and applied to the **HKEY_CURRENT_USER** portion of the registry. The User folder contains an Applications sub-folder.
	- **User/Applications**: Contains the application advertisement script files (.aas) that are used by the operating system-based installation service. These files are applied to users.
	- **Machine**: Includes a Registry.pol file that contains the registry settings that are to be applied to computers. When a computer initialises, this Registry.pol file is downloaded and applied to the **HKEY_LOCAL_MACHINE** portion of the registry. The Machine folder contains an Applications sub-folder.
	- **Machine/Applications**: Contains the .aas files that are used by the operating system-based installation service. These files are applied to computers.
# Group Policy Container
- GPOs are located at `CN=Policies,CN=System,DC=Dragon,DC=local`
- Can be queried with:
```powershell
PS C:\Users\Administrator> Get-ADObject -LDAPFilter '(ObjectClass=GroupPolicyContainer)' -Properties * | select CN,gPCFileSysPath,DisplayName | format-list

CN             : {31B2F340-016D-11D2-945F-00C04FB984F9}
gPCFileSysPath : \\doctor.local\sysvol\doctor.local\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}
DisplayName    : Default Domain Policy

CN             : {6AC1786C-016F-11D2-945F-00C04fB984F9}
gPCFileSysPath : \\doctor.local\sysvol\doctor.local\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}
DisplayName    : Default Domain Controllers Policy
```
- GPOs are linked to the OUs and Sites using it's `gPlink` property
```powershell
PS C:\Users\Administrator> Get-ADObject -LDAPFilter '(ObjectClass=OrganizationalUnit)' -Properties * | select CanonicalName,gpLink | format-list
CanonicalName : doctor.local/Domain Controllers
gpLink        : [LDAP://CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=doctor,DC=local;0]
```
- A Computer or User looks for it's GPO in it's OU's `gPlink` property.