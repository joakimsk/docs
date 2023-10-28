 
Basic setup of Polaric APRSD
============================

The most important settings can be done via a web-interface. You should do this as soon as the server is installed. You may choose to install webapp2 or you may start immediately directly with aprsd using the link: http://localhost:8081/config_menu (if your browser is on another computer than the server, you can replace *'localhost'* with the address of the server-computer). The package initially comes with an admin-user (*‘admin’*) with password *‘polaric’*. You should of course change the password. You may use the command shell::

    polaric-passwd admin

You will be prompted twice for new password. You may also change your password using the webapp.. 

Alternatively: If you have installed *webapp2*, and this is up and running, you can use your web-browser to see the map and some menus. You can click the icon which looks like a lock. Log on with username and password. If this succeeds you should get a brief welcome message and be redirected back to the map. Then click the main menu and select *'Admin/configuration'*. To change your password, see the menu: *'User/password'*. Note that any user can change his/her own password. The superuser (*'admin'*) can change password of any other user as well.

The web-browser may ask you to accept popup menus. You have to do this to be able to continue configuring. It a popup window still do not appear, go to the main-menu again and select *'Admin/configuration'*. You will get a new browser-window with a menu on the left side and status-information on the right side.

Basic server settings
---------------------

To start the setup, choose *'Server Config'* in the left menu. You will now see a set of values that can be changed. They are fairly self-explanatory. The first thing to do is to set your own callsign at the top and restart aprsd. The other settings can wait a little. They are as follows:

* **Data channels settings**. The server is initially set up with one active APRS-IS channel and one inactive radio channel (to be configured for your particular radio). These two channels should be sufficient for most users. Advanced users can add more channels if needed.

* **Igate settings**. *Polaric-aprsd* is capable of running as a full igate if required. If you set up the server with both internet and TNC/Radio it *may* be useful to activate the igate. Don't activate it if you don't need it. The igate use the channels whose names are entered as the primary RF and APRS-IS channels. Make sure that these are correctly configured *before* you activate the igate.

* **Connection to another Polaric Server instance** for remote control and synchronization of SAR information (alias, tags, etc..). Servers to be connected, need to agree on a common secret (authentication key). *This is for advanced users*.

When you are done with all settings on this page, click *'Update'* and you can move on to another set of settings. *'Own position'* is about sending out the position of the server (if you connect a GPS device). You can actually use it as a tracker (with smart beaconing). *'Display on map'* should work for most users without changes. 

.. image:: img/sc1.png


Configuration of data channels
------------------------------

The APRS-IS channel is already set up and activated. The figure shows how it looks like: The channel may for example use the server *`aprs.no`*, port 14585 which automatically delivers APRS traffic from Norway. Users in other countries should change the APRS-IS server. You should also add a *passcode* which will allow you to send data to APRS-IS as well. This code is generated based on your callsign. There are programs or services on the net that can generate a passcode for you if you have a valid callsign. You should also set a filter expression that says more specifically what you want from the APRS-IS server. The filter should be set to accept data from your geographical area of interest or types of data you need. Note that this filter is necessary if you use the general port 14580. See more information about such filters. 

The radio channel is not activated by default. If you need to use a TNC or a radio with builtin APRS, you can configure and activate this channel. Here are the settings:

* **Type**: ``TNC2`` for TNC2 compatible TNC. Use this for Kenwood radios with builtin APRS. Use ``KISS`` for TNCs in KISS mode. It is also possible to use ``TCPKISS`` (KISS over internet). A channel need to be deactivated before changing the type. After changing type, click 'Update' to get the right fields to fill in (you may also need to reload the page).
    
* **Port**: Serial port or USB-serial port, as they are named in Linux. For instance, *`/dev/ttyS0`* would correspond to the COM1 serial port. If you plug in a USB serial converter or a radio with a USB plug, the port name will typically *`/dev/ttyUSB0`* or *`/dev/ttyACM0`* depending on what hardware is used. One way to find out what port-name is assigned to a USB device is to type *'dmesg'* in the command shell after the device is plugged in to look at the system log.

* **Baud-rate** for serial port.

If type is ´´TCPKISS´´, you fill in the IP-address (or server domain name) and a port number. ``TCPKISS`` is usefull e.g. if using *Polaric-aprsd* along with *Direwolf*. Actually, it may be used to access a TNC over a serial device, using a serial to network proxy like *ser2net* or *tcptty*. This can be more flexible and reliable than using the serial port directly from Polaric aprsd and is therefore recommended.    

.. image:: img/sc2.png


Positioning
-----------

*Polaric-aprs* can be set up to send position reports like a tracker. It will only send such reports on APRS-IS unless you explicitly allow it to send it on RF. A digipeater path would be used on RF. You may use a GPS on a serial port and you may also use the NMEA packets from the GPS to adjust the clock. If GPS is not used or if it doesn't get a fix, we may use a default fixed position (given in UTM format). 

A smart-beaconing algorithm will be used when position changes. The frequency of the reporting will depend on the spead and direction of the movement. Min-pause is the minimum time between transmissions. Max-pause is the maximum time between transmissions. Min-distance is the distance (should be called max-distance) moved before a transmission is generated and max-turn is the maximum change in direction before a transmission is generated. 

.. image:: img/sc3.png


To activate the settings
------------------------

For each page of settings click the *'Update'* button to save your changes. To actually use the changes the server will typically need to be restarted. If a restart is necessary it will be clearly indicated. Click on the *'Restart'* button.
