- Two types of authentication protocols available in AD:
	- NTLM
	- Kerberos

[SPNEGO](#SPNEGO) is used to agree on which authentication protocols can/should be used.

# GSS-API
- GSS-API: Generic Security Service - Application Programming Interface
- API that is implemented by security packages to provide __authentication__.

Some GSS-API:
- `gss_acquire_cred` : returns a handle for credentials

GSS-API also handles integrity and confidentiality:
- `gss_get_mic`: Calculates the MIC (message integrity code) for a message
- `gss_verify_mic`: Checks MIC to verify the message integrity
- `gss_wrap`: Attach MIC to a message and optionally encrypt the message content.
- `gss_unwrap`: Verify the MIC and decrypts the message.

# SSPI
- Security Support Provider Interface
- Microsoft's version of GSS-API with some extensions

# Windows SSP
- Security Support Providers in form of DLLs. 
- Implements [SSPI](#SSPI), so that it can used by applications (clients, servers).
### Kerberos SSP
- `kerberos.dll`
- Manages kerberos authentication.
- Caches kerberos tickets and keys
### NTLM SSP
- `msv1_0.dll`
- Manages NTLM authentication.
- Caches NTLM hashes.
- Can be extracted from lsass process.
### Negotiate SSP
- `secur32.dll`
- Intermediary SSP than manages [SPNEGO](#SPNEGO) negotiation and delegates authentication to NTLM SSP or Kerberos SSP based on negotiation.
### Digest SSP
- `wdigest.dll`
- Implements the Digest Access Protocol.
- Used for HTTPs.
### Secure Channel SSP
- `schannel.dll`
- Provides TLS/SSL for HTTPS communication.
### Cred SSP
- `credssp.dll`
- Creates TLS channel, authenticates the client through Negotiate SSP and allows client to send credentials to the server. 
- Used by RDP, in NLA(Network Level Authentication) mode, verifies and authenticate before beginning the RDP session.

# SPNEGO
- Simple and Protected (GSS-API)  Negotiation. 
- Mechanism that allows client and server to negotiate authentication mechanism that is GSS-API compliant. (NTLM or Kerberos).
- Windows uses microsoft's version of `SPNEGO`, called `SPNG`.