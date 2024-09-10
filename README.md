# Radioactive Protocol Specifications

This document outlines the communication protocol used by the Radioactive Protocol through the WebSocket server.
(HIGHLY UNFINISHED LARGE CHANGES MAY COME ABOUT AND MAJORITY HAS YET TO BE FINISHED OR DOCUMENTED)

Approximate Progress: ▓▓▓░░░░░░░░░░░░░░░░░ 15%

## Message Header Types
    Start Packet     End Packet
- `<BEGIN>`   (`<#BEGIN>`)
- `<START>`   (`<#START>`)
- `<END>`   (`<#END>`)
- `<INFO>`   (`<#INFO>`)
- `<ERROR>`   (`<#ERROR>`)
- `<P2P>`   (`<#P2P>`)
- `<DISCONNECT>`   (`<#DISCONNECT>`)
- `<CONTINUE>`   (`<#CONTINUE>`)

## Message Header Information

- `<BEGIN>`: Used during initial connection to exchange public keys and establish a shared session key for AES encryption.
- `<START>`: Used to initiate a task, depending on the header data *TYPE* it can be used to initialize rewriting, P2P, etc.
- `<END>`: Server closing packet that safely closes the communication between server and client.
- `<INFO>`: Never sent by itself; always sent above another message header that contains information about the following packet that the server can use.
- `<ERROR>`: Sent only if a critical error occurs and sending the packet that was meant to be sent isn't an option.
- `<P2P>`: Used to send P2P messages to peers and other servers.
- `<DISCONNECT>`: Client closing packet that safely closes the communication between server and client.
- `<CONTINUE>`: Keep-alive packet that informs the server that the client is still alive if no messages are sent after x interval.

## Header Data Types
'*' Means data isn't specific to a type of value or all types haven't been documented/finished.

- **ID** (UUID)
- **TYPE** (*)
- **ESTABLISHED** (TRUE/FALSE/AWAITING)
- **DATA** (*)
- **PARSEDDATA** (*)
- **ERROR** (*), (ERROR CODE)
- **PEER** (*)
- **CLIENT** (*)
- **PEER DATA** (*)
- **ENCRYPT**: (PUBLIC KEY for session key generation during `<BEGIN>`)
- **VERIFY**: (ENCRYPTED TEST DATA for validation during `<BEGIN>`)
- **SESSION_KEY**: (DERIVED SESSION KEY for message encryption)
- **VERIFIED**: (TRUE/FALSE)

Note that some of these data types are exclusive to specific message headers, as detailed further below.

## Header Data Types Information

- **ID**: UUIDv6 used to identify communication for a specific instance.
- **TYPE**: Determines the type of message (e.g., P2P, REWRITE).
- **ESTABLISHED**: `<BEGIN>` only, indicates if the connection was properly established.
- **DATA**: Varies based on `TYPE`.
- **PARSEDDATA**: For REWRITE type only, returns rewritten data (JavaScript).
- **ERROR**: For `<ERROR>` only, includes the error message followed by the error code.
- **PEER**: For `<INFO>` and `<P2P>` types, indicates peer IP/domain.
- **CLIENT**: For `<INFO>` only, used to send specific client information, depends on `TYPE`.
- **PEER DATA**: For `<P2P>` only, includes data sent by the peer.
- **ENCRYPT**: For `<BEGIN>` only, includes the public key for E2EE session key generation.
- **VERIFY**: For `<BEGIN>` only, includes encrypted test data to verify that the public keys and session key have been correctly exchanged.
- **SESSION_KEY**: For encrypted messages, derived from the exchanged public keys.
- **VERIFIED**: For `<BEGIN>` only, indicates if the public key exchange and session key establishment were successful.

## Establishing Communication

### Example: Server Initiates Communication

*Example, Sent from server:*
```plaintext
<BEGIN>
ID: UUID*
ESTABLISHED: AWAITING
ENCRYPT: SERVER PUBLICKEY*
<#BEGIN>
```
*Example Client Response:*
```plaintext
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFY: ENCRYPTED TESTDATA*
ENCRYPT: CLIENT PUBLICKEY*
<#BEGIN>
```
*Example Server Response:*
```plaintext
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFIED: TRUE
SESSION_KEY: DERIVED SESSION KEY*
<#BEGIN>
```
*Example Client Response:*
```plaintext
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFIED: TRUE
SESSION_KEY: DERIVED SESSION KEY*
<#BEGIN>
```
