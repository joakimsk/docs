Welcome to Polaric Server documentation!
========================================

The "Polaric Server" is mainly a web based service to present live tracking information (APRS, AIS, etc) on maps and where the information is updated in realtime. It is originally targeted for use by radio amateurs in voluntary search and rescue service in Norway. It consists of a web application and a server program (APRS daemon). It runs on e.g. aprs.no as a online service on the internet, but what if we could bring it with us out in the field in a portable computer, possibly with its own LAN, APRS modems and radios, all in a box, ready for use? And how well would it work without always being online with a good connection to the internet?

Main features
-------------

* The user interface is a web application which allows users to browse maps, view and manipulate real-time tracking information using a standard web browser.

* It can integrate live tracking information from various sources. Currently running with APRS and AIS feeds.

* It is a GIS software that can use many open raster layer sources like e.g. WMS, WMTS, Google Maps or OpenStreetmap. It supports caching/storage of map tiles on server for offline use. It supports vector sources like WFS as well, and file formats like GPX or GeoJSON. It can seamlessly switch between multiple map projections.

* Authorized users can, on the server, add information (including APRS objects) and manipulate how objects are displayed in a view shared by all users. For example choice of icons, use descriptive labels (tactical callsigns), tagging, hiding of unnecessary information, etc..

* Support for drawing features directly on the map. Support for drawing distance circles (bike wheel model).

* Plugin with database support (PostGIS) for safe storage of important information and storage and retrieval of tracking (trails) from the past. Very useful for looking up information about past missions.

* To help dealing with information overload, servers can be set up with programmable filters and automatic tagging (sysadm can set up rules) to configure what items are displayed as well as how the items are displayed. For example we have full control over trail-lengths, use of labels, etc. Users can select from a set of predefined filters.

* Support for APRS messages and bulletins. Support for APRS telemetry (can show graphs)

* Can be set up with multiple server instances that can synchronize labels, tags, etc using authenticated APRS messages. Can operate offline with cached map-data. Suitable for small devices like e.g. Raspberry PI and mobile use.

* Based on Open Source software. Open REST API for interoperability.

* Supports role-based authorization of users. Highly configurable what groups of users may see and do.


Copyright and license
---------------------

Polaric Server is free software. See GNU General Public License v.2 and GNU AGPL v.3) Some parts of the source code may use compatible licenses like BSD. Copyright is owned by the respective authors of the software. We also encourage crediting the radio amateur (HAM) community and the Norwegian Radio Relay League for deveopment of APRS technology. If used for a publicly available online service and you make improvements or additions we encourage you to publish these improvements as open source. “APRS” is a registered trademark of Bob Bruninga.

Contents
--------

.. toctree::

   gettingstarted
   usage
   config
   interface
