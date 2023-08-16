 
Client authentication
=====================

Clients (users) with web-browsers, can log in to the server using the username and a password. 

Log in
------

The user can log in by pointing the browser to 'formLogin' with the page origin as a query parameter. If the origin was http://mypage.org the link for logout is like this::
    
    http://localhost:8081/formLogin?origin=<http://mypage.org

Where localhost:8081 can be replaced with whatever location and port your server runs with. The server will return a login form like this. 

.. image:: img/loginform.png

If login is successful, it will return (redirect) the user's browser to the origin page where the login was initiated from. A session will be created and last to the user logs out explicitly or the server is restarted. 

Log out
-------

The user can logout by pointing the browser to 'logout' with the page origin as a query parameter. If the origin was http://mypage.org the link for logout is like this::

    http://localhost:8081/logout?url=http://mypage.org


Server-Server authentication
============================

Another authentication scheme is made for other servers needing to access REST APIs or Websocket interfaces. In this case we do not have users in the same sense. This authentication scheme can also be used for IoT devices. In the current version, there is just one level of authorisation. 

For each REST call (or Websocket message) we attach a SHA-256-HMAC (message authentication code). Computed from the message content, a nonce (number that changes for each call) and a secret shared key. 

This authentication-scheme is currently experimental and may change. 


HTTP Requests
-------------

A request carrying a REST API call should have two headers: 

**Arctic-Nonce:** A number used only once. Servers receiving this can check if it is heard before and dismiss the request if it is. This is a effective protection against replay-attacks.

**Arctic-Hmac:** The SHA-256-HMAC checksum computed from a secret key and a combination of the nonce and the message-content. For POST and PUT requests this is the request body. For GET and DELETE requests it is empty. The Hmac code is encoded with Base-64 and truncated. We use the 44 first characters. When a Hmac is received the server-side also computes a Hmac the same way and compares. If it is the same result, it means that the request is authenticated. 


