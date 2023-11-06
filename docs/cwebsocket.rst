
Client Websocket Services
=========================

In the Webservice/REST API, two websockets are offered to clients. They are: 

`/jmapdata`
    Request a websocket for real-time map overlay updates with trackers, etc. It is open for all, but login- and authorized users can receive different content. 
    
`/notify`
    Request a websocket for notifications, etc. This is a generic publish/subscribe service. 
    

Websocket for map overlay updates
---------------------------------

This service will provide real-time updates of map-information. Typically APRS points and trails. Clients can send `SUBSCRIBE` commands over the websocket to request updates. When the server receives such a command, it will start sending updates to the client::

    SUBSCRIBE filter, x1, x2, x3, x4, scale [, tag] 

Where the parameters are: 

 * **filter** - name of the filter to be used at the server. Corresponds to the filter menu on the webapp2 client.
 * **x1** - upper limit (north) of the area of interest (degrees latitude -90 to 90).
 * **x2** - right limit (east) of the are of interest (degrees longitude).
 * **x3** - lower limit (souith) of the area of interest (degrees latitude -90 to 90).
 * **x4** - left limit (west) of the area of interest (degrees longitude)
 * **scale** - scale of the map as it is displayed on the screen.
 * **tag** (optional) - limit the selection of tracking-obbjects to items having this tag set.


Update format
^^^^^^^^^^^^^
Updates are sent to client when there is a change within the area of interest. The frequency of updates is limited to max 1 per 10 seconds. Updates are in JSON format. Note that when the connection is made and *before* a `SUBSCRIBE` command is issued, an empty **JsOverlay** object is sent to client (where points, delete, lines and pcloud is null). 

**JsOverlay** (top level object):

============== ===================== ==================================================================
 view	        String	              Filter name
 authorization	**AuthInfo** 	      Login and authorization info
 sarmode	    boolean	              True if SAR mode is active
 points	        List of **JsPoint**   Items (may be null or empty)
 delete	        List of String	      Idents (callsigns) of items to be deleted (may be null or empty)
 lines	        List of **JsLine** 	  Lines to be drawn on map (may be null or empty)
 pcloud	        List of **JsTPoint**  Simple points to be plotted on map (may be null or empty)
============== ===================== ==================================================================


**AuthInfo** (Info about authorization level and services):

============== ===================== ==================================================================
 servercall	    String	              Callsign of server
 services      	List of String	      Services active on server. 'basic' is always there. Plugins may add more…
 userid	        String	              *User-id* if logged in. *null* if not logged in.
 groupid        String                *Group-id* if logged in. *null* if not logged in.
 callsign       String                HAM radio callsign of logged in user. *null* if not set or not logged in.
 tagsAuth       String                Authorization based on tags (null if not logged in)
 admin	        boolean	              *true* if authorized as superuser
 sar	        boolean	              *true* if authorized as SAR user
============== ===================== ==================================================================


**JsPoint** (Tracker, etc.): 

============== ===================== ==================================================================
 ident	        String	              Identifier of item (typically this is the callsign)
 pos	        Pair of float	      Position (long, lat) of item.
 title	        String	              Description
 redraw	        boolean	              *True* if item is changing (need to be redrawn on map)
 own	        boolean	              *True* if item is an APRS object owned by this server.
 aprs           boolean               *True* if this is APRS
 telemetry      boolean               *True* if point has APRS telemetry
 sarAuth        boolean               *True* if user has authorization to change point.
 icon	        String	              File path of icon
 href           String                URL if sign (otherwise null or empty)
 label	        **JsLabel** 	      Label of item (may be null or empty)
 trail	        **JsTrail** 	      Trail (last movements) of moving item (may be null or empty)
============== ===================== ==================================================================


**JsLine** (Line between two positions):

============== ===================== ==================================================================
 ident	        String	              Ident of points that are connected by line ( '.' is used as separator)
 from	        Pair of float	      Geographical point where line starts (lon, lat)
 to	            Pair of float	      Geographical point where line ends (lon, lat)
 type	        String	              Some indication what the line represents.
============== ===================== ==================================================================


**JsLabel** (Label of a tracker point):

============== ===================== ==================================================================
 id	            String	              Identifier (callsign) or alias (tactical callsign) to be shown in label
 style	        String	              Style (CSS class) to be used with label
 hidden	        boolean	              True if label is to be hidden
============== ===================== ==================================================================


**JsTrail** (series of points representing movement): 

============== ======================= ==================================================================
 style	        List of String	        Colours (line, point) to be applied to trail when drawn on map
 linestring     List of **JsTPoint**    Trail - list of timestamped points
============== ======================= ==================================================================


**JsTPoint** (point of a trail):

============== ===================== ==================================================================
 pos	        Pair of float	      Geographical position (lon, lat)
 time	        Date	              Timestamp (ISO format)
 path           String                Digipeater path (if APRS)
============== ===================== ==================================================================


    

Websocket for notifications
---------------------------

This service will provide various notifications, primarily from servers to clients. Clients can subscribe to different 'rooms' provided by servers. Clients that subscribe to a room get notifications posted to that room by the server (or other clients). This is a kind of general publish/subscribe service. Clients can send the following commands to the server over the websocket::

    SUBSCRIBE room
    UNSUBSCRIBE room
    PUT room,argument



