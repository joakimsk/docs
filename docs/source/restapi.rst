 
REST API for clients
====================

This document summarises the RESTful API to the Polaric-aprsd backend server. This is used by clients running in standard web-browsers. By default it accepts HTTP requests on port 8081. It is also possible to configure a frontend webserver to act as a proxy for this, so that all HTTP requests to the server-side can go through the standard HTTP port (80). Servers where this is done do therefore not normally need to expose port 8081 externally. A cookie-based session scheme is used for logins. This means that if a user logs on a server a session is established that will used for subsequent REST requests. A session lasts until logging out or the server is restarted. For security I recommend to always use HTTPS if clients and servers are on different LANs. Cross-site requests are possible within certain limits. CORS is supported.

The URL identifies the resource (or service) to be invoked. For REST APIs, the URL identifies the resource and the operation to be performed on it is determined by the HTTP method: GET (read object), PUT (update), POST (add, post), DELETE (remove it). Where a representation of the state is required or returned, the JSON format is used. Some operations that perform search expect query parameters.

Most services will require authorization: O=open, L=login, S=SAR, A=admin.



System services
---------------
Source: `AdminApi.java` and `ShellScriptApi.java`

+------------------------+-------+-+------------------------------------------------------+
|`/authStatus`           | GET   |O| Get authorization info. Returns AuthInfo object      |
+------------------------+-------+-+------------------------------------------------------+
|`/system/tags`          | GET   |O| Get tags used                                        |
+------------------------+-------+-+------------------------------------------------------+
|`/system/ownpos`        | GET   |A| Get position of this server (symbol, latlong pos)    |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |A| Update position of this server (symbol, latlong pos) |
+------------------------+-------+-+------------------------------------------------------+
|`/system/sarmode`       | GET   |S| SAR mode info. null means SAR mode is off            |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |S| Update SAR mode info. null to turn it off            |
+------------------------+-------+-+------------------------------------------------------+
|`/system/icons/{dir}`   | GET   |O| List of icons available in a subdirectory            |
+------------------------+-------+-+------------------------------------------------------+
|`/scripts`              | GET   |A| Get list of available commands/scripts               |
+------------------------+-------+-+------------------------------------------------------+
|`/scripts/{script-id}`  | GET   |A| Execute a script/command                             |
+------------------------+-------+-+------------------------------------------------------+



Users and clients
-----------------
Source: `UserApi.java`

+------------------------+-------+-+------------------------------------------------------+
|`/filters`              | GET   |L| Get filters available for you                        |
+------------------------+-------+-+------------------------------------------------------+
|`/mypasswd`             | PUT   |L| Change your own password                             |
+------------------------+-------+-+------------------------------------------------------+
|`/wsclients`            | GET   |A| Get (websocket) clients                              |
+------------------------+-------+-+------------------------------------------------------+
|`/loginusers`           | GET   |A| Get logged in users (list of userids)                |
+------------------------+-------+-+------------------------------------------------------+
|`/groups`               | GET   |A| Get available groups (roles)                         |
+------------------------+-------+-+------------------------------------------------------+
|`/usernames`            | GET   |L| Get list of all users (userids only)                 |
+------------------------+-------+-+------------------------------------------------------+
|`/users`                | GET   |A| Get list of all users                                |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |A| Add a user                                           |
+------------------------+-------+-+------------------------------------------------------+
|`/users/{id}`           | GET   |A| Get info about a given user                          |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |A| Update a user                                        |
|                        +-------+-+------------------------------------------------------+
|                        | DELETE|A| Remove a user                                        |
+------------------------+-------+-+------------------------------------------------------+



Items (tracker objects)
-----------------------
Source: `ItemApi.java`

+------------------------+-------+-+------------------------------------------------------+
|`/item/{id}/info`       | GET   |O| Get info about a tracker                             |
+------------------------+-------+-+------------------------------------------------------+
|`/item/{id}/trail`      | GET   |O| Get trail of moving tracker. List of points.         |
+------------------------+-------+-+------------------------------------------------------+
|`/item/{id}/reset`      | PUT   |S| Reset trail and other info about item                |
+------------------------+-------+-+------------------------------------------------------+
|`/item/{id}/chcolor`    | PUT   |S| Change color of trail.                               |
+------------------------+-------+-+------------------------------------------------------+
|`/item{id}/tags`        | GET   |O| Get list of tags set on an item                      |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |S| Add a tag to an item                                 |
+------------------------+-------+-+------------------------------------------------------+
|`/item/{id}/tags/{tag}` | DELETE|S| Remove a tag from an item                            |
+------------------------+-------+-+------------------------------------------------------+
|`/items`                | GET   |O| Search items (query parameters)                      |
+------------------------+-------+-+------------------------------------------------------+
|`/item/{id}/alias`      | GET   |S| Alias for tracker with id (callsign).                |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |S| Set alias for tracker with id (callsign).            |
+------------------------+-------+-+------------------------------------------------------+



APRS Telemetry
--------------
Source: `ItemApi.java`

+-------------------------+-------+-+------------------------------------------------------+
|`/telemetry/{id}/descr`  | GET   |O| Get telemetry description for a given item           |
+-------------------------+-------+-+------------------------------------------------------+
|`/telemetry/{id}/meta`   | GET   |O| Get telemetry metadata for a given item              |
+-------------------------+-------+-+------------------------------------------------------+
|`/telemetry/{id}/current`| GET   |O| Get telemetry current report for a given item        |
+-------------------------+-------+-+------------------------------------------------------+
|`/telemetry/{id}/history`| GET   |O| Get telemetry history report for a given item        |
+-------------------------+-------+-+------------------------------------------------------+



Own APRS objects 
----------------
Source: `AprsObjectApi.java`

+------------------------+-------+-+------------------------------------------------------+
|`/aprs/objects`         | GET   |S| Get the list of active objects (owned by this server)|
|                        +-------+-+------------------------------------------------------+
|                        | POST  |S| Add an object                                        |
+------------------------+-------+-+------------------------------------------------------+
|`/aprs/objects/{id}`    | PUT   |S| Update an object                                     |
|                        +-------+-+------------------------------------------------------+
|                        | DELETE|S| Remove an object                                     |
+------------------------+-------+-+------------------------------------------------------+



Short messages
--------------
Source: `MailBoxApi.java`

+------------------------+-------+-+------------------------------------------------------+
|`/mailbox`              | GET   |L| Get content of mailbox (list of messages)            |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |L| Post a message                                       |
+------------------------+-------+-+------------------------------------------------------+
|`/mailbox/{msg-id}`     | DELETE|L| Delete a message from mailbox                        |
+------------------------+-------+-+------------------------------------------------------+



APRS Bulletins
--------------
Source: `BullBoardApi.java`

+-----------------------------------------+-----+-+-------------------------------------------------+
|`/bullboard/groups`                      | GET |O| List of active bulletin groups                  |
+-----------------------------------------+-----+-+-------------------------------------------------+
|`/bullboard/{groupid}/senders`           | GET |O| List of callsigns of senders to a given group   |
+-----------------------------------------+-----+-+-------------------------------------------------+
|`/bullboard/{groupid}/messages`          | GET |O| Get all messages in a group                     |
|                                         +-----+-+-------------------------------------------------+
|                                         | POST|L| Submit a bulletin                               |
+-----------------------------------------+-----+-+-------------------------------------------------+
|`/bullboard/{groupid}/messages/{sender}` | GET |O| Bulletins from a given sender in a group        |
+-----------------------------------------+-----+-+-------------------------------------------------+

.. http:get:: /bullboard/groups

   Returns a list of active bulleti groups
   
   :status 200: Ok
   :>jsonarr string id: Group id 

   
.. http:get:: /bullboard/(groupid)/senders

   Returns a list of callsigns that have posted messages to the group
   
   :status 200: Ok
   :status 404: Group not found.
   
   :>jsonarr string id: Callsign of sender 


.. http:get:: /bullboard/(groupid)/messages

   Returns all messages in a group. Note that this returns a list of lists
   
   :status 200: Ok
   :status 404: Group not found.
   
   :>jsonarr string id: Callsign of sender 
   :>jsonarr Bulletin[]: List of bulletins (several attributes)
   
   
.. http:post:: /bullboard/(groupid)/messages

   Post a bulletin to a group

   :status 200: Ok  
   :status 401: No callsign registered for user.
   :status 400: Cannot parse input
   
   :<json string bullid: Index for bulletin
   :<json string groupid: Group identifier
   :<json string text: Text of bulletin
   

.. http:get:: /bullboard/(groupid)/messages(sender)

   Returns messages in a group posted by a specific sender. Note that this returns a list of lists
   
   :status 200: Ok
   :status 404: Group not found.
   
   :>jsonarr string id: Callsign of sender 
   :>jsonarr Bulletin[]: List of bulletins (several attributes)

   
   
SAR (Search and Rescue)
-----------------------
Source: `SarApi.java`

+------------------------+-------+-+------------------------------------------------------+
|`/sar/ipp`              | GET   |L| Get a list of IPPs (with distance rings) for user    |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |L| Add a IPP (with distance rings)                      |
+------------------------+-------+-+------------------------------------------------------+
|`/sar/ipp/{id}`         | GET   |L| Get a specific IPP                                   |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |L| Update a specific IPP                                |
|                        +-------+-+------------------------------------------------------+
|                        | DELETE|L| Remove a IPP                                         |
+------------------------+-------+-+------------------------------------------------------+

.. http:get:: /sar/ipp

   Returns a list of IPPs for the given user.
   
   :status 200: Ok
   :status 500: No authorization info found.
   
   :>jsonarr string id: Unique identifier 
   :>jsonarr string descr: Descripttion of IPP
   :>jsonarr double[] pos: Position of IPP (longitude, latitude)
   :>jsonarr float p25: Radius (meters) of 25% distance ring 
   :>jsonarr float p50: Radius (meters) of 50% distance ring 
   :>jsonarr float p75: Radius (meters) of 75% distance ring 
   :>jsonarr float p95: Radius (meters) of 95% distance ring 
   
   
.. http:post:: /sar/ipp

   Add a IPP

   :status 200: Ok  
   :status 500: No authorization info found.
   :status 400: Cannot parse input
   
   :<json string id: Unique identifier for ipp
   :<json string descr: Description of IPP
   :<json double[] pos: Position of IPP (longitude, latitude)
   :<json float p25: Radius (meters) of 25% distance ring 
   :<json float p50: Radius (meters) of 50% distance ring    
   :<json float p75: Radius (meters) of 75% distance ring 
   :<json float p95: Radius (meters) of 95% distance ring 

   
.. http:get:: /sar/ipp/(id)

   Returns a specific IPP
   
   :parameter id: Unique indentifier of IPP
   
   :status 200: Ok
   :status 500: No authorization info found.
   :status 404: Not found.
   
   :>json string id: Unique identifier 
   :>json string descr: Descripttion of IPP
   :>json double[] pos: Position of IPP (longitude, latitude)
   :>json float p25: Radius (meters) of 25% distance ring 
   :>json float p50: Radius (meters) of 50% distance ring 
   :>json float p75: Radius (meters) of 75% distance ring 
   :>json float p95: Radius (meters) of 95% distance ring 
   
   
      
.. http:put:: /sar/ipp/(id)

   Update a specific IPP
   
   :parameter id: Unique indentifier of IPP
   
   :status 200: Ok
   :status 500: No authorization info found. 
   :status 400: Cannot parse input
   :status 404: Not found.
   
   :<json string id: Unique identifier 
   :<json string descr: Descripttion of IPP
   :<json double[] pos: Position of IPP (longitude, latitude)
   :<json float p25: Radius (meters) of 25% distance ring 
   :<json float p50: Radius (meters) of 50% distance ring 
   :<json float p75: Radius (meters) of 75% distance ring 
   :<json float p95: Radius (meters) of 95% distance ring 
   
   

.. http:delete:: /sar/ipp/(id)
   
   Delete an IPP
   
   :parameter id: Unique indentifier of IPP

   :status 200: Ok
   :status 500: No authorization info found.

