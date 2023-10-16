 
Client authentication
=====================

In version 3.0 the login scheme is replaced with a scheme more suited for REST API and mobile-apps. 
Clients (users) with web-browsers or mobile-apps, can log in to the server using the username and a password. The client code may call a method POST to a url ``directLogin`` with the form parameters: 'username' and 'password'. If login is successful, a session-key (base64 encoded, 64 characters long), will be returned and can be used in authenticating subsequent requests to the server as well when establishing websocket connections. This key should be treated as a secret. It is not persistent, so when the server reboots users have to log-in again. 

After a successful login the client can call the GET method on the url ``authStatus`` which will return info on server capabilities and what authorizations the users have. If authentication fails, it returns an error code (401 unauthorized). If authentication fails, a GET on an alternative ``authStatus2`` can be used to get some information about the server-session anyway.  


Login using polaric-webapp2 client
----------------------------------

In the webapp, login can be done with a widget (popup) like this:

.. image:: img/loginform2.png

If login is successful (the ``authStatus`` method returns sucessfully, the widget will change and a logout button will be available. In addition it is possible to temporarily change the role (if user is authorized for that). Here, logout will simply mean to remove the session key so that the subsequent REST calls will not do proper authentication.

.. image:: img/loggedin.png


HTTP requests
-------------

A request carrying a REST API can use the Authorization HTTP header the following way:: 

 Authorization: Arctic-Hmac userid;nonce;hmac;role

The data field consists of the following (separated by semicolon): 

**userid**
    The username used in the login
**nonce** 
    A number unique for each REST call. The webapp generates a 64 bit (8 bytes) random number. This is base64 encoded. 
**hmac** 
    A HMAC hash using the key returned by the ``directLogin`` call and a concatenation of the nonce and a 
    SHA256 hash of the request body if it exists. The HMAC is base64 encoded.
**role** (optional)
    We can add a temporary role-id if user is authorized for more than one role. 

Websocket creation
------------------

When opening a websocket connection we can authenticate by adding the string *userid;nonce;hmac;role* (where *;role* is optional) as described above as a request parameter to the wss URL. If authentication is successful the server will threat communication over the resulting websocket as authenticated and with the resulting authorizations. 



Server-Server authentication
============================

*(this is experimental and will probably change in version 3.0 - see also client authentication above)*

Another authentication scheme is made for other servers needing to access REST APIs or Websocket interfaces. It is also used for access from IoT devices. This authentication scheme don't currently identify users (persons). In the current version, there is just one level of authorisation. 

For each REST call (or Websocket message) we attach a SHA-256-HMAC (message authentication code). Computed from the message content, a nonce (number that is different for each call) and a secret shared key. 


HTTP Requests
-------------

A request carrying a REST API call can have two headers specific for this authentication mechamism: 

**Arctic-Nonce:** A number used once. 8 byte random number, encoded with Base64. Servers receiving this can check if it is heard before and dismiss the request if it is. This is a effective protection against replay-attacks and ensures that each HMAC is based on a unique message. 

**Arctic-Hmac:** The SHA-256-HMAC checksum computed from a secret key and a combination of the nonce and the message-content. For POST and PUT requests this is the request body. For GET and DELETE requests it is empty. The Hmac code is encoded with Base-64 and truncated. We use the 44 first characters. When a Hmac is received the server-side also computes a Hmac the same way and compares. If it is the same result, it means that the request is authenticated. 

Secret Key
----------

In the current version the secret key is stored on each server participating: In ``/etc/polaric/aprsd/server.ini``. ``system.auth.key`` setting. It is recommended to use a secure random function to generate this. 32 or 64 bytes is the recommended length of the key. Base64 encoded keys are ok. 

