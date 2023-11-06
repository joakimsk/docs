 
Database plugin REST API
========================

This document summarises the RESTful API to the Polaric-aprsd backend server, provided by the database plugin. This is used by clients running in standard web-browsers. 

The URL identifies the resource (or service) to be invoked. For REST APIs, the URL identifies the resource and the operation to be performed on it is determined by the HTTP method: GET (read object), PUT (update), POST (add, post), DELETE (remove it). Where a representation of the state is required or returned, the JSON format is used. Some operations that perform search expect query parameters.

Most services will require authorization: O=open, L=login, S=SAR, A=admin.

See more info on `Client Authentication <https://polaricserver.readthedocs.io/en/latest/clientauth.html#client-authentication>`_. To access ‘L’, ‘S’ or ‘A’ services, requests must be authenticated. To access ‘O’ services, we do not use authentication.


Trackers API
------------

Source: TrackerApi.java

+------------------------+-------+-+-----------------------------------------------------------+
|`/trackers`             | GET   |L| Get  'my trackers'  for user.                             |
|                        +-------+-+-----------------------------------------------------------+
|                        | POST  |L| Add a tracker (for logged-in user)                        |
+------------------------+-------+-+-----------------------------------------------------------+
|`/trackers/{id}`        | DELETE|L| Delete a tracker for the logged in user                   |
|                        +-------+-+-----------------------------------------------------------+
|                        | PUT   |L| Update a tracker (for logged in user)                     |
+------------------------+-------+-+-----------------------------------------------------------+
|`/trackers/tags`        | GET   |L| Get tracker tags for the logged in user                   |
|                        +-------+-+-----------------------------------------------------------+
|                        | POST  |L| Add a tag to the logged in user's trackers                |
+------------------------+-------+-+-----------------------------------------------------------+
|`/trackers/tags/{id}`   | DELETE|L| Delete a tag for the logged in user                       |
+------------------------+-------+-+-----------------------------------------------------------+

.. http:get:: /trackers

   Returns a list of tracker objects owned by user or by the user given as request parameter 'user'. 

   :form user: (optional) Userid of user to get trackers for. If not given, use the identity of user that performs the request.
   :status 200: Ok
   :status 401: Authentication failed
   :status 400: No authorization info found
   :status 500: If something went wrong with the database SQL query
   
   :>json string id: The callsign of the tracker
   :>json string user: Userid of the owner
   :>json string alias: Alias to be used when active. Null if not set
   :>json string icon: Icon to be used when active. Null if not set
   :>json boolean active: True if active and visible
   :>json date lastHrd: When it was last heard

   
.. http:post:: /trackers

   Add a tracker to the list of tracker objects owned by user. 

   :form user: (optional) Userid of user to get trackers for. If not given, use the identity of user that performs the request.
   :status 200: Ok
   :status 401: Authentication failed
   :status 400: No authorization info found
   :status 400: Cannot parse input
   :status 403: Not allowed to manage this tracker
   :status 403: Item is managed already
   :status 500: If something went wrong with the database SQL query
   
   :<json string id: The callsign of the tracker
   :<json string user: Userid of the owner
   :<json string alias: Alias to be used when active. Null if not set
   :<json string icon: Icon to be used when active. Null if not set
   :<json boolean active: True if active and visible
   :<json date lastHrd: When it was last heard


.. http:delete:: /trackers/(id)

   Remove a tracker from user's list

   :parameter string id: Callsign/ident of the tracker 
   :status 200: Ok
   :status 401: Authentication failed
   :status 404: Item not found
   :status 403: Item is owned by another user
   :status 400: No authorization info found
   :status 500: Server error or if something went wrong with the database SQL query
   
   
.. http:put:: /trackers/(id)

   Update a tracker 

   :status 200: Ok
   :status 400: Input parsing error
   :status 400: Authorization info not found
   :status 400: User xxx not found
   :status 403: Item not owned by user 
   :status 500: If something went wrong with the database SQL query, 
   
   :<json string id: The callsign of the tracker
   :<json string user: Userid of the owner
   :<json string alias: Alias to be used when active. Null if not set
   :<json string icon: Icon to be used when active. Null if not set
   :<json boolean active: True if active and visible
   :<json date lastHrd: When it was last heard


.. http:get:: /trackers/tags

   Returns a list of tracker tags. Tags will be applied to all trackers owned by user. 

   :status 200: Ok
   :status 401: Authentication failed
   :status 400: No authorization info found
   :status 500: If something went wrong with the database SQL query
   :>jsonarr string tag: Tag

   
.. http:post:: /trackers/tags

   Add tags to be applied to trackers owned by user. 

   :status 200: Ok
   :status 401: Authentication failed
   :status 400: No authorization info found
   :status 500: If something went wrong with the database SQL query
   :<jsonarr string tag: Tag
   
   
   
   
Signs API
---------

Source: SignsApi.java

+------------------------+-------+-+------------------------------------------------------+
|`/signs`                | GET   |L| Get all signs                                        |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |L| Add a sign                                           |
+------------------------+-------+-+------------------------------------------------------+
|`/signs/{id}`           | GET   |L| Get a specific sign                                  |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |L| Update a sign                                        |
|                        +-------+-+------------------------------------------------------+
|                        | DELETE|L| Remove a sign                                        |
+------------------------+-------+-+------------------------------------------------------+
|`/signs/types`          | GET   |O| Get a list of types (categories)                     |
+------------------------+-------+-+------------------------------------------------------+


.. http:get:: /signs

   Returns a list of sign objects

   :status 200: Ok
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   :>jsonarr string id: Unique id for sign
   :>jsonarr string url: Link to a web-page or image
   :>jsonarr string descr: Description 
   :>jsonarr string icon: Filename of icon
   :>jsonarr long scale: Scale of map from which sign is to be visible
   :>jsonarr int type: Category of sign
   :>jsonarr string tname: Type name
   :>jsonarr double[] pos: Position of sign (longitude, latitude)
   

.. http:post:: /signs

   Add a sign

   :status 200: Ok
   :status 400: Cannot parse input
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   :<json string id: Unique id for sign
   :<json string url: Link to a web-page or image
   :<json string descr: Description 
   :<json string icon: Filename of icon
   :<json long scale: Scale of map from which sign is to be visible
   :<json int type: Category of sign
   :<json string tname: Type name
   :<json double[] pos: Position of sign (longitude, latitude)

   
   
.. http:get:: /signs/(id)

   Returns a given sign objects
   
   :parameter string id: Unique dent of the sign
   
   :status 200: Ok
   :status 404: Object not found
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   :>json string id: Unique id for sign
   :>json string url: Link to a web-page or image
   :>json string descr: Description 
   :>json string icon: Filename of icon
   :>json long scale: Scale of map from which sign is to be visible
   :>json int type: Category of sign
   :>json string tname: Type name
   :>json double[] pos: Position of sign (longitude, latitude)
   
   
   
.. http:put:: /signs/(id)

   Update a given sign object
   
   :parameter string id: Unique ident of the sign
   
   :status 200: Ok
   :status 404: Object not found
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   :<json string id: Unique id for sign (will be ignored)
   :<json string url: Link to a web-page or image
   :<json string descr: Description 
   :<json string icon: Filename of icon
   :<json long scale: Scale of map from which sign is to be visible
   :<json int type: Category of sign
   :<json string tname: Type name
   :<json double[] pos: Position of sign (longitude, latitude)   
   
   
   
   
.. http:delete:: /signs/(id)

   Remove a given sign objects if it exists
   
   :parameter string id: Unique ident of the sign
   
   :status 200: Ok
   :status 400: Object not found
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   
   
.. http:get:: /signs/types

   Returns a list of valid categories for signs
   
   :status 200: Ok
   :status 500: If something went wrong with the database SQL query
   
   :>jsonarr int id: Unique numerical id 
   :>jsonarr string name: Descriptive name of category
   :>jsonarr string icon: Filename of icon

   
   
   
Historical search API
---------------------

Source: HistApi.java

+------------------------------------+-------+-+------------------------------------------------------+
|`/hist/{id}/aprs`                   | GET   |O| Get APRS raw packets for a given callsign            |
+------------------------------------+-------+-+------------------------------------------------------+
|`/hist/{id}/trail`                  | GET   |O| Get historical trail for a given callsign            |
+------------------------------------+-------+-+------------------------------------------------------+
|`/hist/{id}/hrdvia`                 | GET   |O| Get points heard via a callsign                      |
+------------------------------------+-------+-+------------------------------------------------------+
|`/hist/snapshot/{x1}/{x2}/{x3]/{x4}`| GET   |O| Get snapshot (area, time)                            |
+------------------------------------+-------+-+------------------------------------------------------+
   
   
.. http:get:: /hist/(id)/aprs

   Returns a list of received APRS packets for a given callsign. Timespan can be given.
   
   :parameter string id: APRS callsign
   :form n: Max number of packets to be returned
   :form tto: (optional) End of timespan to search (if not given or "-/-" it means now) [1]_
   :form tfrom: (optional) Start of timespan to search [1]_
   
   :status 200: Ok
   :status 400: Cannot parse number or time-string
   :status 500: If something went wrong with the database SQL query
   
   :>jsonarr Date time: Timestamp for packet or when received 
   :>jsonarr string source: Source channel
   :>jsonarr string from: Sender callsign
   :>jsonarr string from: Destination callsign (e.g. "APRS")
   :>jsonarr string via: Digipeater/igate path
   :>jsonarr string report: The APRS report (content of packet)



.. http:get:: /hist/(id)/trail

   Returns a trail a list of positions for a given callsign. Timespan *must* be given. It returns a `JsOverlay` JSON object to be presented as a overlay on the map.
   
   :parameter string id: APRS callsign
   :form n: Max number of points to be returned
   :form tto: (optional) End of timespan to search (if not given or "-/-" it means now) [1]_
   :form tfrom: (optional) Start of timespan to search [1]_
   
   :status 200: Ok
   :status 400: Cannot parse time-string
   :status 500: If something went wrong with the database SQL query
   
   
.. http:get:: /hist/(id)/hrdvia

   Returns a list of positions from where traffic have been received by the callsign. Timespan *must* be given. It returns a `JsOverlay` point-cloud JSON object to be presented as a overlay on the map.
   
   :parameter string id: APRS callsign
   :form n: Max number of points to be returned
   :form tto: (optional) End of timespan to search (if not given or "-/-" it means now) [1]_
   :form tfrom: (optional) Start of timespan to search [1]_
   
   :status 200: Ok
   :status 400: Cannot parse time-string
   :status 500: If something went wrong with the database SQL query
   
   
   
.. http:get::  /hist/snapshot/(x1)/(x2)/(x3)/(x4)
   
   Returns a list of positions, trails, etc. in a given geographical area at a given time instant. It returns a `JsOverlay` to be presented as a overlay on the map. The choice of colours for the trails is remembered between calls from the same user and can be reset. 
   
   :parameter double x1: West latitude limit (left of the map)
   :parameter double x2: East latitude limit (right of the map)
   :parameter double x3: South longitude limit (bottom of the map)
   :parameter double x4: North longitude limit (top of the map)
    
   :form tto: Date and time [1]_
   :form filter: View filter to be applied
   :form reset: Reset colours used for the trails 
    
   :status 200: Ok
   :status 400: Cannot parse number or time-string
   :status 500: If something went wrong with the database SQL query
   
     
     

.. [1] Format for time is "yyyy-MM-dd/HH:mm"     
     
     
Json Object API
---------------

Source: RestApi.java

The server does not interpret the content of raw objects (encoded in JSON). Clients may use this API to store different things. Tags can be used to give info what the objects contain. 

+----------------------------+-------+-+------------------------------------------------------+
|`/objects/{tag}`            | GET   |L| Get a list of objects for the logged in user         |
|                            +-------+-+------------------------------------------------------+
|                            | POST  |L| Add a (raw text) object for the logged in user       |
+----------------------------+-------+-+------------------------------------------------------+
|`/objects/{tag}/{id}`       | GET   |L| Get a single (raw text) object                       |
|                            +-------+-+------------------------------------------------------+
|                            | PUT   |L| Update a (raw text) object                           |
|                            +-------+-+------------------------------------------------------+
|                            | DELETE|L| Remove an object                                     |
+----------------------------+-------+-+------------------------------------------------------+
|`/objects/{tag}/{id}/share` | GET   |L| Get users with which the object is shared            |
|                            +-------+-+------------------------------------------------------+
|                            | POST  |L| Add a sharing of the object                          |
|                            +-------+-+------------------------------------------------------+
|                            | DELETE|L| Remove a sharing of the object                       |
+----------------------------+-------+-+------------------------------------------------------+


.. http:get:: /objects/(tag)

   Returns a list of objects with the given tag and for the logged in user. 
   
   :parameter string tag: Tag that denotes a type or category of object
   
   :status 200: Ok
   :status 401: Authentication required.
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   :>jsonarr string id: Ident of the object
   :>jsonarr boolean readOnly: Object should be treated as read-only 
   :>jsonarr boolean noRemove: Object shouldn't be removed...
   :>jsonarr string data: Object in raw JSON (or XML)
   
   
   
.. http:post:: /objects/(tag)

   Add a object with the given tag and for the logged in user. The request body should contain a raw text representation of the object to be added. It is not parsed, but will in most cases be in JSON format. Returns the numeric identifier of the newly posted object.
   
   :parameter string tag: Tag that denotes a type or category of object
   
   :status 200: Ok
   :status 401: Authentication required.
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.   
   
   

.. http:get:: /objects/(tag)/(id)

   Get a single object having the given id and tag. A raw text is returned (in most cases in JSON format)
   
   :parameter string tag: Tag that denotes a type or category of object
   :parameter int id: Numeric identifier for object
   
   :status 200: Ok
   :status 400: Cannot parse id, must be numeric.
   :status 401: Authentication required.
   :status 404: Object not found
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.

   
.. http:put:: /objects/(tag)/(id)

   Update a single object having the given id and tag. The request body should contain a raw text representation of the object to be added. It is not parsed, but will in most cases be in JSON format.
   
   :parameter string tag: Tag that denotes a type or category of object
   :parameter int id: Numeric identifier for object
   
   :status 200: Ok
   :status 400: Cannot parse id, must be numeric.
   :status 401: Authentication required.
   :status 404: Object not found
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   
.. http:delete:: /objects/(tag)/(id)

   Remove a single object having the given id and tag (if it exists).
   
   :parameter string tag: Tag that denotes a type or category of object
   :parameter int id: Numeric identifier for object
   
   :status 200: Ok
   :status 400: Cannot parse id, must be numeric.
   :status 401: Authentication required.
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.   
   

   
   
.. http:get:: /objects/(tag)/(id)/share
   
   Get a list of users (or groups) with which the given object is shared
   
   :parameter string tag: Tag that denotes a type or category of object
   :parameter int id: Numeric identifier for object
      
   :status 200: Ok
   :status 400: Cannot parse id, must be numeric.
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
    
   :>jsonarr string id: Ident of the user
   :>jsonarr boolean readOnly: User has read-only access
   
   
   
   
.. http:post:: /objects/(tag)/(id)/share
   
   Add a user (or group) with which the given object is shared.
   
   :parameter string tag: Tag that denotes a type or category of object
   :parameter int id: Numeric identifier for object
      
   :status 200: Ok
   :status 400: Cannot parse id, must be numeric.
   :status 401: You are not authorized for the requested sharing
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
    
   :<jsonarr string id: Ident of the user
   :<jsonarr boolean readOnly: User has read-only access   
   
   
   
.. http:delete:: /objects/(tag)/(id)/share/(userid)
   
   Remove a sharing with a user for the given object (unlink)
   
   :parameter string tag: Tag that denotes a type or category of object
   :parameter int id: Numeric identifier for object
      
   :status 200: Ok
   :status 400: Cannot parse id, must be numeric.
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.

   
   
   
