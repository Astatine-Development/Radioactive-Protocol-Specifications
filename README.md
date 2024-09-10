# Radioactive Protocol Specifications

This document outlines the communication protocol used by the Radioactive Protocol through the WebSocket server.
(HIGHLY UNFINISHED LARGE CHANGES MAY COME ABOUT AND MAJORITY HAS YET TO BE FINISHED OR DOCUMENTED)

Aproximate Progress: ▓▓▓░░░░░░░░░░░░░░░░░ 15%

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

- `<BEGIN>`:  Only used durring inital connection, used to confirm connection and start AES encryption.
- `<START>`: Used to initate a task, depending on the header data *TYPE* it can be used to intialize rewritting, p2p, etc.
- `<END>`: Server Closing packet that safetly closes the communication between server and client.
- `<INFO>`:  Never sent by itself, always sent above another message header that contains information about the following packet that the server can use.
- `<ERROR>`:  Only sent if a critical error was found and sending the packet that was ment to be sent isnt an option.
- `<P2P>`:  Used to send P2P messages to peers and other servers.
- `<DISCONNECT>`: Client Closing packet that safetly closes the communication between server and client.
- `<CONTINUE>`: Keep alive packet that informs the server that the client is still alive if no messages are sent after x interval.


## Header Data Types
'*' Means data isnt specific to a type of value or all types havent been documented/finished.

- **ID** (UUID)
- **TYPE** (*)
- **ESTABLISHED** (TRUE/FALSE/AWAITING)
- **DATA** (*)
- **PARSEDDATA** (*)
- **ERROR** (*), (ERROR CODE)
- **PEER** (*)
- **CLIENT** (*)
- **PEER DATA** (*)
- **ENCRYPT**: (SHAREDKEY*)
- **VERIFY**: (ENCRYPTED TEST DATA*)
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
- **ENCRYPT**: For `<BEGIN>` only, includes the public key for E2EE encryption before it is enabled.
- **VERIFY**: For `<BEGIN>` only, includes dummy data (e.g., test) encrypted with the key from `ENCRYPT`.
- **VERIFIED**: For `<BEGIN>` only, indicates if the data from `VERIFY` was decoded successfully and the message was understood.

## Establishing Communication

### Example: Server Initiates Communication

*Example, Sent from server, * means more information later/below/above, it will not be included in a real request* 
```
<BEGIN>
ID: UUID*
ESTABLISHED: AWAITING
ENCRYPT: SERVER PUBLICKEY*
<#BEGIN>
```
*Example Client Response* 
```
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFY: TESTDATA*
ENCRYPT: CLIENT PUBLICKEY*
<#BEGIN>
```
*Example Server Response* 
```
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFIED: TRUE
ENCRYPT: CLIENT PUBLICKEY*
<#BEGIN>
```
*Example Client Response* 
```
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFIED: TRUE
ENCRYPT: SERVER PUBLICKEY*
<#BEGIN>
```
Once the communication is established, all messages are encrypted using with the pre-shared keys.
