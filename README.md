# Protocol Specifications:
Details on how the Radioactive Protocol Communicates through the websocket server.

## Message Header Types:
- `<BEGIN>` (<#BEGIN>)
- `<START>` (<#START>)
- `<END>` (<#END>)
- `<INFO>` (<#INFO>)
- `<ERROR>` (<#ERROR>)
- `<P2P>` (<#P2P>)
- `<DISCONNECT>` (<#DISCONNECT>)

## Header Data types:
 - ID (UUID)
 - TYPE (*)
 - ESTABLISHED (TRUE/FALSE/AWAITING)
 - DATA (*)
 - PARSEDDATA (*)
 - ERROR (*),(ERROR CODE)
 - PEER (*)
 - CLIENT (*)
 - PEER DATA (*)
 - ENCRYPT: (AES256SHAREDKEY*)
 - VERIFY: (ENCRYPTED TEST DATA*)
 - VERIFIED: (TRUE/FALSE)
Note that some of these are exclusive to specific message headers, described further down.

## Establish Communication:

*Example, Sent from server, * means more information later/below/above, it will not be included in a real request* 
```
<BEGIN>
ID: UUID*
ESTABLISHED: AWAITING
ENCRYPT: SHAREDKEY*
<#BEGIN>
```
*Example Client Response* 
```
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFY: TESTDATA*
<#BEGIN>
```
*Example Server Response* 
```
<BEGIN>
ID: SAME UUID*
ESTABLISHED: TRUE
VERIFIED: TRUE
<#BEGIN>
```
Communication has now been properly established, from now on all messages are encrypted with AES256 using the preshared key.

## Header data types information:
 - ID -- UUIDv6 used to determine communication for a specific instance.
 - TYPE -- Determines the type of message (EX: p2p, rewrite).
 - ESTABLISHED -- `<BEGIN>` only, Used to determine if connection was properly established.
 - DATA -- Depends on TYPE*
 - PARSEDDATA -- REWRITE type only, returns rewritten data (JS).
 - ERROR -- `<ERROR>` only, Error message followed by code.
 - PEER -- `<INFO>` only, P2P type only, peer ip/domain.
 - CLIENT -- `<INFO>` only, used to send specific client information, depends on TYPE.
 - PEER DATA -- `<P2P>` only, P2P type only, includes data that peer sent.
 - ENCRYPT: -- `<BEGIN>` only, includes key for AES256 encryption before its enabled.
 - VERIFY: -- `<BEGIN>` only, includes dummy data (ex: test) thats encrypted with the key from ENCRYPT type.
 - VERIFIED: -- `<BEGIN>` only, If true then the data from VERIFY was decoded successfully and the message was understood.
