---
tags:
  - general
aliases: 
cssclasses:
---
[Understanding Kerberos In Deep](https://www.vaadata.com/blog/what-is-kerberos-kerberos-authentication-explained/)

**Basic Concepts** :-

It is not the role of the Kerberos protocol to check whether the client is authorized to access the requested service. This responsibility is left to the service.
Kerberos is responsible only for authentication and not authorization

`krbtgt` : Kerberos Ticket Granting Ticket 
It is a special user account in AD which is created by default when domain is set up.
The security of the Kerberos protocol depends on the confidentiality of the `krbtgt` secret , because this is the key which is used to sign and encrypt TGTs and other Kerberos Related messages.

**Domain Controller has KDC : Kerberos Distribution Center**
1. Authentication Server (AS)
   - This is responsible for Authenticating user and services.
   - Returns TGT 

2. Ticket Granting Server (TGS)
  - It takes TGT tickets and issues TGS which are service tickets
   - Part of Service Ticket Phase
##### Application Server (AP)

- The **actual target service** (e.g. file server, MSSQL , Printer etc.)
- Verifies the **service ticket** to allow access

REQ : Request
REP : Response

Every Ticket has a **Time-Stamp** for which ticket granted is valid

**TGT : Ticket Granting Ticket** - This is received during authentication phase and which consist of demonstrating KDC that client's secret is known.

At the end , client has a session key and TGT ticket which is required to prove its authentication later

**TGS : Ticket Granting Service**  - This is obtained during Ticket Granting Service phase , in which a client is requesting access to a service using TGT and session key previously obtained and targeting existing SPN.

![[Kerberos.svg]]


1. **AS-REQ** : Time stamp encrypted with NTLM hash to request for a signed TGT is sent to the Key Distribution Center (KDC) on the DC.
2. **AS-REP** : Initial request is encrypted with the krbtgt account hash and sent back to the client in the form of a Ticket-Granting-Ticket
3. **TGS-REQ** : The TGT is sent to the KDC to request a Ticket Granting Service ticket.
4. **TGS-REP** : The TGT is encrypted with the service account's hash and sent back to the client in the form of a TGS
5. **AP-REQ** : The TGS is sent to KDC to request a Service to be provided.

# PHASE 1 - Ticket Granting Ticket 
#### AS-REQ : Authentication Service - Request 
Client --> KDC ( Domain Controller )

- Username , Timestamp is sent.
- Username + Timestamp + Password is encrypted with key derived from password (NTLM).

#### AS-REP : Authentication Service - Reply
Client <-- KDC

- TGT - Ticket Granting Ticket is sent back to client along with Session Key.
- TGT is encrypted using krbtgt account hash.

![[Pasted image 20250721004921.png]]
# PHASE 2 - Ticket Granting Service

#### TGS-REQ : Ticket Granting Service - Request
Client --> KDC

- Username + timestamp +  TGT + SPN (Service Principal No.) is sent.
- SPN contains the service and server name we intend to access.

#### TGS-REP : Ticket Granting Service - Reply
Client <-- KDC

 - TGS Ticket + Service Session Ticket is obtained in return.
 - TGS is encrypted using key derived from service owner hash.

![[Pasted image 20250721004929.png]]
# PHASE 3 - Using Application Service

#### AP-REQ : Application Service - Request
Client --> KDC 

- Sends TGS to target service 
- Sends Service Session Ticket to prove its identity

#### AP-REP : Application Service - Reply ( optional )
Client <-- KDC

- The service may respond with
- A message encrypted with the session key
- Confirms mutual authentication (client ↔ server)


![[Pasted image 20250721004935.png]]

The service will use its configured account's password hash to decrypt the TGS and validate the Service Session Key.


