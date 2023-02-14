 
Database plugin REST API
========================

This document summarises the RESTful API to the Polaric-aprsd backend server, provided by the database plugin. This is used by clients running in standard web-browsers. 

The URL identifies the resource (or service) to be invoked. For REST APIs, the URL identifies the resource and the operation to be performed on it is determined by the HTTP method: GET (read object), PUT (update), POST (add, post), DELETE (remove it). Where a representation of the state is required or returned, the JSON format is used. Some operations that perform search expect query parameters.

Most services will require authorization: O=open, L=login, S=SAR, A=admin.



Trackers API
------------

Source: TrackerApi.java

+------------------------+-------+-+------------------------------------------------------+
|`/trackers`             | GET   |L| Get  'my trackers'  for user                         |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |L| Add a tracker (for logged in user)                   |
+------------------------+-------+-+------------------------------------------------------+
|`/trackers/{id}`        | DELETE|L| Delete a tracker for the logged in user              |
|                        +-------+-+------------------------------------------------------+
|                        | PUT   |L| Update a tracker (for logged in user)                |
+------------------------+-------+-+------------------------------------------------------+
|`/trackers/tags`        | GET   |L| Get tracker tags for the logged in user              |
|                        +-------+-+------------------------------------------------------+
|                        | POST  |L| Add a tag to the logged in user's trackers           |
+------------------------+-------+-+------------------------------------------------------+
|`/trackers/tags/{id}`   | DELETE|L| Delete a tag for the logged in user                  |
+------------------------+-------+-+------------------------------------------------------+

.. http:get:: /trackers

   Returns a list of tracker objects owned by user. 

   :form user: (optional) Userid of user to get trackers for. If not given, use the identity of user that performs the request.
   :status 200: Ok
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
   :status 500: If something went wrong with the database SQL query or if authorization info was not found.
   
   
.. http:put:: /trackers/(id)

   Update a tracker 

   :status 200: Ok
   :status 400: Input parsing error, authorization of user or owner of tracker not found
   :status 403: Tracker not owned by user 
   :status 500: If something went wrong with the database SQL query, 
   
   :<json string id: The callsign of the tracker
   :<json string user: Userid of the owner
   :<json string alias: Alias to be used when active. Null if not set
   :<json string icon: Icon to be used when active. Null if not set
   :<json boolean active: True if active and visible
   :<json date lastHrd: When it was last heard

   
   
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
+------------------------+-------+-+------------------------------------------------------+
|`/signs/types`          | GET   |O| Get a list of types (categories)                     |
+------------------------+-------+-+------------------------------------------------------+



Historical search API
---------------------

Source: HistApi.java

+------------------------------------+-------+-+------------------------------------------------------+
|`/hist/{id}/aprs`                   | GET   |O| Get APRS raw packets for a given callsign            |
+------------------------------------+-------+-+------------------------------------------------------+
|`/host/{id}/trail`                  | GET   |O| Get historical trail for a given callsign            |
+------------------------------------+-------+-+------------------------------------------------------+
|`/host/{id}/hrdvia`                 | GET   |O| Get points heard via a callsign                      |
+------------------------------------+-------+-+------------------------------------------------------+
|`/host/snapshot/{x1}/{x2}/{x3]/{x4}`| GET   |O| Get snapshot (area, time)                            |
+------------------------------------+-------+-+------------------------------------------------------+


Object API
----------

Source: RestApi.java

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
