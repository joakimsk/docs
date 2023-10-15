 
Client authentication
=====================

In version 3.0 the login scheme is replaced with a scheme more suited for REST API and mobile-apps. 
Clients (users) with web-browsers or mobile-apps, can log in to the server using the username and a password. The client code will call a method POST to a url 'directLogin' with the form parameters: 'username' and 'password'. If login is successful, a session-key (base64 encoded, 64 characters long), will be returned that is used in authenticating subsequent requests to the server as well as the websocket connections. The key should be treated as a secret. It is not persistent, so when the server reboots users have to log-in again. 

After as successful login the client can call the GET method on the url 'authStatus' which will return info on server capabilities and what authorizations the users have. If authentication fails it returns an error code (401 unauthorized). If authentication fails, a GET on an alternative authStatus2 can be used to get some information about the server-session anyway.  


Login using polaric-webapp2 client
----------------------------------

In the webapp, login will look like this

.. image:: img/loginform2.png

If login is successful (the getAuth method returns sucessfully, the widget will change and a logout button will be available. Here, logout will simply mean to remove the session key. 

.. image:: img/loggedin.png


Server-Server authentication
============================

(this will probably change in version 3.0)

Another authentication scheme is made for other servers needing to access REST APIs or Websocket interfaces. It is also used for access from IoT devices. This authentication scheme don't currently identify users (persons). In the current version, there is just one level of authorisation. 

For each REST call (or Websocket message) we attach a SHA-256-HMAC (message authentication code). Computed from the message content, a nonce (number that is different for each call) and a secret shared key. 

This authentication-scheme is currently experimental and may change. 


HTTP Requests
-------------

A request carrying a REST API call can have two headers specific for this authentication mechamism: 

**Arctic-Nonce:** A number used once. 8 byte random number, encoded with Base64. Servers receiving this can check if it is heard before and dismiss the request if it is. This is a effective protection against replay-attacks and ensures that each HMAC is based on a unique message. 

**Arctic-Hmac:** The SHA-256-HMAC checksum computed from a secret key and a combination of the nonce and the message-content. For POST and PUT requests this is the request body. For GET and DELETE requests it is empty. The Hmac code is encoded with Base-64 and truncated. We use the 44 first characters. When a Hmac is received the server-side also computes a Hmac the same way and compares. If it is the same result, it means that the request is authenticated. 

Secret Key
----------

In the current version the secret key is stored on each server participating: In ``/etc/polaric/aprsd/server.ini``. ``system.auth.key`` setting. It is recommended to use a secure random function to generate this. 32 or 64 bytes is the recommended length of the key. Base64 encoded keys are ok. 

