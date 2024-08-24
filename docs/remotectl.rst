 
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


Group Subscription
^^^^^^^^^^^^^^^^^^

A *Polaric-aprsd* instance (node) can participate in a *group* of such nodes. A node initiates association to at most one other node (the parent) and may receive connection requests from any number of other nodes (children). This means that nodes in a group are associated with each other in a strictly hierarchical structure.

CON
    A node can send a CON message to another node (parent) to subscribe to subsequent updates (it can also send updates). At receipt of a CON request, a node should record the identity of sender and the time of receipt. Then, the sending node is regarded as a member of the group. A node typically sends a CON message to its parent at startup. CON requests can be repeated later to renew the membership of the group, for example every 10 minutes. If no CON request is received for 30 minutes, the node should remove the child from the group (record is purged).

    
Group synchronisation commands
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following requests are used to propagate information to all members of a group. On receipt of such a request, a node should execute it and propagate it further to all other nodes that it knows about (parent + children), except the node that it got it from in the first place.

ALIAS <callsign> <alias> 
    Set an alias for a given callsign.

ICON <callsign> 
    Set an alternative icon for a given callsign. Use filename for graphics without path (location of files is a part of server configuration).

TAG <callsign> <tag> 
    Add a tag to the given point object. A tag is basically a keyword.

RMTAG <callsign> <tag> 
    Remove a tag from the given point object.

USER <userid> 
    Announce that user is logged on to the system. NB: The userid can be encrypted (see below).

RMUSER <userid> 
    Announce that user is logged off. NB: The userid can be encrypted (see below).

RMNODE <ident> 
    Announce that a node is closing down.


Encryption
----------

From v. 2.8 of *Polaric-aprsd* we may *encrypt* userids to comply with privacy regulations (GDPR). It is possible to specify in the setup for which other servers encryption is to be used. If on, the userid is encrypted using AES (128bit) where key is generated from the secret key (the same as used for authenticating messages). An initialisation vector is used, where the two first bytes are random and the third byte is the day of the year (modulo 256). These three bytes are used as a prefix on the encrypted userid. The resulting length is either 144 or 272 bits depending on the length of the id. The encrypted userid (prefixed) is encoded using base64.

Before activating encryption for messages to be sent over amateur radio, be sure to check the HAM radio regulations in your country.

