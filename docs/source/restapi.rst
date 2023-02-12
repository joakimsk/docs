 
REST API
========

This document summarises the RESTful API to the Polaric-aprsd backend server. This is used by clients running in standard web-browsers. By default it accepts HTTP requests on port 8081. It is also possible to configure a frontend webserver to act as a proxy for this, so that all HTTP requests to the server-side can go through the standard HTTP port (80). Servers where this is done do therefore not normally need to expose port 8081 externally. A cookie-based session scheme is used for logins. This means that if a user logs on a server a session is established that will used for subsequent REST requests. A session lasts until logging out or the server is restarted. For security I recommend to always use HTTPS if clients and servers are on different LANs. Cross-site requests are possible within certain limits. CORS is supported.

The URL identifies the resource (or service) to be invoked. For REST APIs, the URL identifies the resource and the operation to be performed on it is determined by the HTTP method: GET (read object), PUT (update), POST (add, post), DELETE (remove it). Where a representation of the state is required or returned, the JSON format is used. Some operations that perform search expect query parameters.

Most services will require authorization: O=open, L=login, S=SAR, A=admin.



System services summary
-----------------------

+------------------------+-------+-+------------------------------------------------------+
|`/authStatus``          | GET   |O| Get authorization info. Returns AuthInfo object      |
+------------------------+-------+-+------------------------------------------------------+
|`/groups``              | GET   |A| Return a list of groups (group-id, group-name)       |
+------------------------+-------+-+------------------------------------------------------+
|`/user`                 | GET   |A| Get the list of users (userid, last used time)       |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |A| Add user                                             |
+------------------------+-------+-+------------------------------------------------------+
|`/user/{id}`            | GET   |A| Get info about a given user                          |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |A| Update info about a given user                       |
|                        +-------+-+------------------------------------------------------+
|                        | DELETE|A| Remove a user                                        |
+------------------------+-------+-+------------------------------------------------------+
|`/usernames`            | GET   |A| Get a list of users (just userids)                   |
+------------------------+-------+-+------------------------------------------------------+
|`/system/tags`          | GET   |O| Get tags used                                        |
+------------------------+-------+-+------------------------------------------------------+
|`/system/ownpos`        | GET   |A| Get position of this server (symbol, latlong pos)    |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |A| Update position of this server (symbol, latlong pos) |
+------------------------+-------+-+------------------------------------------------------+
|`/system/sarmode`       | GET   |S| SAR mode info. null means SAR mode is off.           |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |S| Update SAR mode info. null to turn it off.           |
+------------------------+-------+-+------------------------------------------------------+
|`/system/icons/{dir}`   | GET   |O| List of icons available in a subdirectory.           |
+------------------------+-------+-+------------------------------------------------------+
|`/user/{id}/alias`      | GET   |S| Alias for tracker with id (callsign).                |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |S| Set alias for tracker with id (callsign).            |
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



Own APRS objects 
----------------

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

+------------------------+-------+-+------------------------------------------------------+
|`/mailbox`              | GET   |L| Get content of mailbox (list of messages)            |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |L| Post a message                                       |
+------------------------+-------+-+------------------------------------------------------+
|`/mailbox/{msg-id}`     | DELETE|L| Delete a message from mailbox                        |
+------------------------+-------+-+------------------------------------------------------+



