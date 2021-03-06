# Identity and Chains of Trust


## What is a Certificate Authority?

Nothing about a Fabric network can function unless components trust each other's identities and the permissions those identities entitle them to. Establishing that trust means creating *digital certificates* which can be verified against a shared trust store of identities.

Those certificates are created by a component we call a Certificate Authority (CA) using public keys (which are self-generated from a component -- or user's -- private keys).

This is not dissimilar from how identity works in the real world. Think of your private key as being roughly analogous to a Social Security Number. It's too dangerous to expose directly as a regular form of identity, so what you do is use it to generate other identities -- a driver's license, for example -- which is issued by a trusted source (the DMV).

In a Fabric network, the CA -- which is trusted by the network -- takes the place of the DMV and issues you a digital cert. And just as the driver's license is used as a proxy for identity (while also conferring explicit privileges like the right to drive), the digital certs issued by a CA confer not just identity but rights to perform network functions or over network components.

## Root CAs and Intermediate CAs

CAs come in two flavors: Root CAs and Intermediate CAs.

*(Something I don't yet understand and can't find anything on: why are there two kinds of CAs? Why doesn't everyone just use the Root CA?)*


Notes:

All Hyperledger Fabric CA servers in a cluster share the same database for keeping track of identities and certificates. If LDAP is configured, the identity information is kept in LDAP rather than the database.

A server may contain multiple CAs. Each intermediate CA has a parent CA which is either a root CA or another intermediate CA.

In order to create a CA signing certificate for an intermediate CA, the intermediate CA must enroll with a parent CA in the same way that a fabric-ca-client enrolls with a CA.

Unless the Fabric CA server is configured to use LDAP, it must be configured with at least one pre-registered bootstrap identity to enable you to register and enroll other identities.



*(Other things I'm not sure about: How are you brought into the network originally (how are those certs generated, from where, where are they stored, what happens to them). And then how are they used by you in two different ways: 1) to execute certain functions. How are they tested? Who tests them? Where do they test them? What exact identity are you using (is it a global ID or does it just go back to the root CA to check it). And 2) how is this identity used to get new privileges and how and where is that update stored (such that it can be checked out when you want to perform that action)).*







## Membership Service Providers

Membership Service Providers  are the trust store of the certs and abstract away all cryptographic mechanisms and protocols behind issuing and validating certificates. An MSP may define their own notion of identity, and the rules by which those identities are governed (identity validation) and authenticated (signature generation and verification).

A Hyperledger Fabric blockchain network can be governed by one or more MSPs. This provides modularity of membership operations, and interoperability across different membership standards and architectures.

The configuration of an MSP needs to be specified locally at each peer and orderer (to enable both to sign), and on the channels to enable peer, orderer, client identity validation, and respective signature verification (authentication) by and for all channel members.

When MSP is held outside of any channel it's termed **local MSP**. It is solely used by a process *(I'm fuzzy on this point -- what kind of process?)* but it is only used when it is invoked (by orderers or peers, generally). So when a peer connects to an orderer it uses its local MSP to identify itself. The orderer will use the local MSP to validate the connection. This local MSP information is on the file system and looks structurally identical to MSP on a channel.

Any MSP information in channels is of the same logical structure but in general represents a config related entry, meaning it is specific to the channel. In general this is used when you join a peer to a channel. The peer goes into the config block for the channel and determines the orderer address it needs to connect and all of the MSP information it requires so it can connect to components and validate who they are.

The MSP for the "network" is the MSP for the system channel. The system channel MSP information is held in the config block of the orderer system channel.

MSPs may be configured with a set of root certificate authorities (rCAs), and optionally a set of intermediate certificate authorities (iCAs). An MSP’s iCA certificates must be signed by exactly one of the MSP’s rCAs or iCAs. An MSP’s configuration may contain a certificate revocation list, or CRL. If any of the MSP’s root certificate authorities are listed in the CRL, then the MSP’s configuration must not include any iCA that is also included in the CRL, or the MSP setup will fail.

Each rCA is the root of a certification tree. That is, each rCA may be the signer of the certificates of one or more iCAs, and these iCAs will be the signer either of other iCAs or of user-certificates.

The default MSP implementation accepts as valid certificates signed by the appropriate authorities. Certificates signed by internal nodes will be rejected.




## MSPs in DRIVENET





[Next: Policies for Access Control](./PoliciesforAccessControl.md)
