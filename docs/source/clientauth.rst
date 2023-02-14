 
Client authentication
=====================

Log in
------

The user can log in by pointing the broser to 'formLogin' with the page origin as a query parameter. If the origin was http://mypage.org the link for logout is like this::
    
    http://localhost:8081/formLogin?origin=<http://mypage.org

Where localhost:8081 can be replaced with whatever location and port your server runs with. The server will return a login form like this. 

.. image:: img/loginform.png

If login is successful, it will return the user to the origin page where the login was initiated from. A session will be created and last to the user logs out explicitly or the server is restarted. 

Log out
-------

The user can logout by pointing the browser to 'logout' with the page origin as a query parameter. If the origin was http://mypage.org the link for logout is like this::

    http://localhost:8081/logout?url=http://mypage.org


