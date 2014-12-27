Basic UDP Reliability Protocol (BURP)
=====================================

BURP is a brain-dead simple reliable UDP protocol.  This is optimized to be as
simple and easy to implement as possible.  It is slow, inefficient, and almost
useless.

## BURP header specification

All fields are Big Endian.

Field | Field size in Bits | Representation
----- | ------------       | ------
Flags | 32                 | Bitfield
Size  | 32                 | uint32
Sequence | 32              | uint32
Payload  | Size * 8        | N/A
CRC32    | 32              | CRC32

Flags bits:

Bits    | Name 
------- | ------- 
31 to 1 | Reserved for future use 
0       | ACK 


## Protocol

The sender sends the first message with an arbitrary sequence number, and
increments the sequence number by one on every subsequent message, including
`ACK` messages.  The receiver verifies the checksum and sends an a message in
response with the `ACK` bit set to `1`.  The ACK response has its own sequence
number in line with the other messages that the receiver might be sending to
the sender.  The ACK's payload will always be a 32 bit BE uint32 that is equal
to the original message's sequence number.  The sender, upon receiving this
ACK, verifies the checksum, but does not send an ACK in return.

The sender will not send the next message
