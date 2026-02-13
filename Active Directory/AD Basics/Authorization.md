# ACLs
- Every object has a "security descriptor" in the `NTSecurityDescriptor` property, which is used to check if a user/object has access to that object.
- A Security Descriptor contains the following Property:
	- `Owner SID`: Object Owner
	- `Group SID`: Owner's Primary Group
	- `DACL`: Discretionary Access Control List - Contains ACEs
	- `SACL`: System Access Control List
```powershell
Owner  : DOCTOR\Domain Admins
Group  : DOCTOR\Domain Admins
Access : {System.DirectoryServices.ActiveDirectoryAccessRule,                                                 System.DirectoryServices.ActiveDirectoryAccessRule,                                                            System.DirectoryServices.ActiveDirectoryAccessRule,                                                            System.DirectoryServices.ActiveDirectoryAccessRule...}
audit  :
```

# DACL
Contains ACE that determines the user/group that can can access the object.
### ACE in DACL
Defines access permission on a object for a specific user or group.
An ACE contains the following basic property:
- `AccessControlType`: Specify if the ACE is for Allowing or Denying access
- `IsInherited`: Specify if the ACE is inherited or not (True/False)
- `InheritenceType`: The type of object class that can inherit the ACE from this object.
- `ActiveDirectoryRights`: Indicates the type of access the ACE is applying.
- `IdentityReference`: The principal (User/Group) for which the ACE is applied.
- `ObjectType`: GUID that indicates an extended right, property, or child object depending on the Access Mask Flag. All 0s if not used.
```powershell
ActiveDirectoryRights : ReadProperty
InheritanceType       : None
ObjectType            : 59ba2f42-79a2-11d0-9020-00c04fc2d3cf
InheritedObjectType   : 00000000-0000-0000-0000-000000000000
ObjectFlags           : ObjectAceTypePresent
AccessControlType     : Allow
IdentityReference     : NT AUTHORITY\Authenticated Users
IsInherited           : False
InheritanceFlags      : None
PropagationFlags      : None
```

# SACL
ACEs in SACL defines the access attempt that are going to generate logs. Useful for defence.
### ACE in SACL
Defines audit permission on an object for a specific user or group.

# ActiveDirectoryRights
The [following rights](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/990fb975-ab31-4bc1-8b75-5da132cd4584) can be specified in a ACE:

- `Delete`: Delete the object.
- `ReadControl`: Read the security descriptor, except the SACL.
- `WriteDacl`: Modify the object DACL in the security descriptor.
- `WriteOwner`: Modify the object owner in the security descriptor.
- `CreateChild`: Create child objects. For containers.
- `DeleteChild`: Delete child object. For containers.
- `ListContents`: List child objects. For containers. The object is hidden from user if this right nor ListObject are not granted.
- `ReadProperty`: Read the property or [property set](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/177c0db5-fa12-4c31-b75a-473425ce9cca) specified in object type. If object type is zero, then all properties can be read. It does not allow to read the [confidential properties](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/7c1cdf82-1ecc-4834-827e-d26ff95fb207).
- `WriteProperty`: Modify the property specified in object type. If object type is zero, then all properties can be modified.
- `WritePropertyExtended`: Execute a [validated write](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/20504d60-43ec-458f-bc7a-754eb64446df). Maybe the most interesting [validated write](https://docs.microsoft.com/en-us/windows/win32/adschema/validated-writes) is the [Self-Membership](https://docs.microsoft.com/en-us/windows/win32/adschema/r-self-membership) for groups, that allows to add your current user to the group with the ACE.
- `DeleteTree`: Delete all the child objects with a delete-tree operation.
- `ListObject`: List the object. The object is hidden from user if this right nor ListContents are not granted.
- `ControlAccess`: Special permission that can be interpreted in many different ways, based on the object type. In case the object type is a GUID of a [confidential property](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/e6685d31-5d87-42d0-8a5f-e55d337f47cd), it gives permission to read it. If is the GUID of an [extended right](https://docs.microsoft.com/en-us/windows/win32/adschema/extended-rights) registered in the database schema, then the right is given. In case the object type is null (GUID is all zeros) then all the extended rights are granted.
### Generic Rights
- `GenericRead`: ReadControl, ListContents, ReadProperty (all), ListObject.
- `GenericWrite`: ReadControl, WriteProperty (all), WritePropertyExtended (all).
- `GenericExecute`: ReadControl, ListContents.
- `GenericAll`: Delete, WriteDacl, WriteOwner, CreateChild, DeleteChild, DeleteTree, ControlAccess (all), GenericAll, GenericWrite.
### Extended Rights
- `User-Force-Change-Password`: Change the user password without knowing the current password. For user objects.
- `DS-Replication-Get-Changes`: To replicate the database data. For the domain object. Required to perform dcsync.
- `DS-Replication-Get-Changes-All`: To replicate the database secret data. For the domain object. Required to perform dcsync.

# Things to Remember
- Files, directories, process, registries and services are protected by local DACLs, managed by local computer.
- `Domain Admins` are added to the Local `Administrator` group by default. Hence they can access any local object in that windows computer.