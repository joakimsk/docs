 
Protocol for remote control
===========================

The purpose of this protocol is that multiple APRS nodes (typically multiple instances of the polaric-aprsd) can exchange information or commands. Currently we use this to keep multiple instances of the aprsd synchronised wrt. the following information:

* Aliases or tags associated with point objects (typically APRS stations identified by callsigns).
* Alternative icons for APRS stations or objects, set manually
* SAR-mode activation/deactivation

The synchronisation is done via authenticated APRS messages. Authentication is based on a shared key and sequence numbers. The nodes that synchronise form an overlay network in a strict hierarchy (tree structure).

Authenticated APRS messages
---------------------------

The format of a message is as follows::

 :<recipient><content>#<MAC>{<messsage-id>

**recipient** 
    (as defined in the APRS standard), callsign of the recipient padded with spaces so that its length is excactly 9 characters.

**content**
    The message to be sent

**MAC**
    A checksum is computed by concatenating *a secret key*, the *sender callsign*, the *recipient callsign*, the *content* and the *message-id* and generating a MD5 hash from the resulting byte-string (the characters of the message are UTF-8 encoded before generating the hash). The resulting MD5 hash is *base-64* encoded and the first 8 bytes of the encoded hash is used as the message authentication code (MAC) sent with the message. Both the sender and receiver of a message compute the MAC. On receipt of a message a node should compute the MAC and compare it with the MAC received with the message. If not match, the message should be rejected.

**messsage-id** 
    (as defined in the APRS standard). It should be unique for each message. Polaric APRSD use a sequence-number that is incremented with each message.


    
Acknowledgment messages
-----------------------

On receipt of a message a node should respond with an ACK or REJ message as described in the APRS protocol to indicate if the message was successfully delivered to the application and that a command was successfully executed or not. The sender should retry a message if an acknowledgement is not received within a certain time (but there should be a limit on the number of retries, eg. 3). The recipient node should use the message-id to ensure that the same message is not delivered to the application more than once.

Requests (commands)
-------------------

The content field of a message carry a request consisting of a request identifier and zero or more arguments separated by whitespace characters. A request message should be acknowledgded (ACK message) if successfully processed, or rejected (REJ) if delivery or processing failed.
