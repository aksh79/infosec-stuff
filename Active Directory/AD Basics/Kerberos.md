- Preferred way of authentication in active directory networks for domain control.
- Cannot be used in WORKGROUPS
- Implemented by [Kerberos SSP](Authentication.md#Kerberos%20SSP).
- Uses [Tickets](#Tickets) to authenticate against [Principals](#Principals).

# Kerberos Actors
- `Client`: Entity that receives the ticket.
- `Service`: The __Application Server__ that provides the service to the user.
- `KDC`: Key Distribution Center, the Domain Controller, responsible for creating tickets for the user (Authentication Server + Ticket Granting Server).

# Principals
- [Users](Users.md) & [Services](Services.md)
- To request a ticket for the service, [SPNs](Services.md#SPN) are required.
- Principals are also used to represent the user asking for the ticket using it's `SamAccountName` using `NT-PRINCIPAL` type.S
- `NT-ENTERPRISE` is used to identify users from different domain, using `SamAccountName@DomainFQDN`.

# Tickets
- Tickets are partially encrypted data containing:
	- Target Principal - Usually a service (SPN).
	- Information about client - Name & Domain
	- Key to establish a secure connection
	- Timestamp
 
### PAC
The `Privilege Attribute Certificate` contains the authorization data.
PAC Contains:
- The Client Domain: Domain and [SID](../../Windows/Windows%20Internals/Uncategorized/SID.md) of the domain.
- The Client User: Username and [SID](../../Windows/Windows%20Internals/Uncategorized/SID.md) of the user.
- The Client Group: The groups and RIDs of the groups that the users belongs to.
- Other Groups: Other SIDs that references to non-domain groups. 
The PAC also contains several signatures to maintain the integrity of the PAC.
- Server Signature: Signature of the PAC content created with the key used to encrypt the ticket
- KDC Signature: A signature of the Server Signature created with the KDC key. Could be used to check that the PAC is created by the KDC and not via silver tickets. This is not checked.
- Ticket Signature: Signature of the ticket content created with the KDC key

# Types of Tickets

### Service Ticket
- The ticket that the users provides to the Service/Application Server/Principal.
- KDC issues ST to the user.
- A client can issue an ST for any service registered (via SPNs) in the Domain, as kerberos doesn't handle authorization.
- Only the principal (users/service) can read contents STs, as it includes the session key needed to establish the secure communication between the client and server. 
- STs are encrypted with key of the target principal.
- In AD, principals are usually the service. Hence the STs are encrypted with the key of the account owner of the service. (The account that registered the service via SPN).
- These [Kerberos Keys](Users.md#Kerberos%20Keys) are derived from the password of these service accounts. Hence if these keys are compromised, it can be used to create ticket knows as [Silver Tickets](../Persistence/Silver%20Tickets.md), which can be used to access any service of that user.
- If two tickets are created for two services owned by the same user, it will be encrypted using the same key. Also, the target service (`sname`) is not the encrypted part of the ticket. Hence if we have access to one service of the user. We can change the target service of the user and access other services provided by the user. Hence if have the ticket for administrator to access MSSQL, we can change the the target to SMB (CIFS/machineName), and get administrative shell.

### Ticket Granting Ticket
- Required by the KDC to issue ST for services. Can be thought of as ST for KDC.
- As it should only be read by the principal, in this case KDC, it is encrypted by the key of `krbtgt` account which is the service account that runs the KDC (Also known as the KCD key).
- If we compromise the key or password of `krbtgt` account, we can create arbitrary TGTs for any account known as [Golden Tickets](../Persistence/Golden%20Tickets.md). Golden tickets can be used to impersonate any account in the domain, including Administrators.

# Kerberos Keys
[Kerberos Keys](Users.md#Kerberos%20Keys)
# Ticket Acquisition
```mermaid
sequenceDiagram
	actor Client
	participant KDC
	participant Service
	Client ->> KDC : 1. AS-REQ
	KDC ->> Client: 2. AS-REP (TGT)
	Client ->> Service: 3. Authentication Negotiation (SPNEGO)
	Client ->> KDC: 4. TGS-REQ (TGT)
	KDC ->> Client: 5. TGS-REP (ST)
	Client ->> Service: 6. AP-REQ (ST)
	Service ->> KDC: 7. KERB-VRFY-PAC-REQ
	KDC ->> Service: 8. PAC Response
	Service ->> Client: 9. AP-RES
	
```
1. `AS-REQ`: Client sends the timestamp encrypted with it's [Kerberos Keys](Users.md#Kerberos%20Keys). This is called pre-authentication, and sometimes it is not required.
2. `AS-REP`: The server responds back with two information. TGT encrypted with KDC key and client data encrypted with client key.
3. `Negotiation`: Using [SPNEGO](Authentication.md#SPNEGO), client negotiates the authentication protocol (either NTLM or Kerberos).
4. `TGS-REQ (TGT)`: Client sends the TGT and the [SPN](Services.md#SPN) of the target service, to the KDC and asks for the service ticket.
5. `TGS-REP (ST)`: KDC decrypts the TGT using it's key and gets access to the username and session key. KDC uses the session key to decrypt the username and verify if everything is correct. The KDC then sends two information of the client, ST of the request service, encrypted with the the key of the service user and the client data encrypted with the session key.
6. `AP-REQ (ST)`: Client sends the ST received in the last step to the service. The service uses it's key and decrypts the ST and gets the service session key which is used to establish secure communication. It also gets the PAC data after decrypting the ST, which specifies if the user has the privilege to access the resource. 
7. `KERB-VRFY-PAC-REQ`: (OPTIONAL). This step only occurs when the the service wants to validate the PAC. It sends the PAC to the KDC to verify it's authenticity, as it is signed using KDC keys.
8. `PAC Response`: The KDC responds with the result of the verificaiton.
9. `AP-RES` : This step is also Optional. The service responds to the `AP-REQ` using the session key it got from ST to prove that service can decrypt the ST.

# Kerberos Services
- `KDC`: Runs on port 88 (TCP/UDP)
- `Kpasswd`: Runs on port 464 (TCP/UDP). Used to change password of users. 

# Kerberos Formats
### Linux
Kerberos keys in stored in `keytab` files, usually located at `/etc/krb5.keytab`. Linux uses `.ccache` file format to save kerberos keys. 
### Windows
Windows uses the `kirbi` format to stores the kerberos keys. They are stored in the lsass memory. Tickets are transmitted over the network using this format. [ticket_converter.py](https://github.com/Zer1t0/ticket_converter) can be used to convert `.ccache` to `krb`.
It is also possible to create kerberos keys from users' password using [Get-KerberosAESKey.ps1](https://gist.github.com/Kevin-Robertson/9e0f8bfdbf4c1e694e6ff4197f0a4372).

# Kerberos Delegations
Used when for example, a web-server wants to access another service on behalf of the user. The webserver impersonates a user and performs actions on a third service.
### Unconstrained Delegation
User sends TGT along with ST so that the service can impersonate the user by requesting ST for a third service, using the TGT
### Constrained Delegation
Uses `Service For User (S4U)` extension that allows a service to impersonate a user without using TGT, for specific services.

### Anti Delegation Measures
To prevent delegations, two methods can be used:
- `NOT_DELEGATED` flag in [User Account Control](Users.md#User%20Account%20Control) attribute.
- User belongs to `Protected Groups`.

