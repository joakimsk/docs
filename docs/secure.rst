
Securing the server
===================


With the basic installation, we use HTTP between the client and the server. This may be good enough when they are on the same LAN/subnet and the LAN/subnet is small and we kind of trust the computers and users here. If not, we should consider securing the client server communication. If using HTTPS (HTTP over TLS/SSL), we ensure that the communication is encrypted.

If clients are anywhere on the internet and communication is crossing subnets, the communication **should** be secured. In addition, using a separate port for the backend, may not work since firewalls and routers may block this port. 

Setting up aprsd with HTTPS
---------------------------

As long as we do not go through routers or firewalls where port 8081 (or whatever port is used to the backend) is blocked, we can still use it but with HTTPS. The tricky part is the certificate. As soon we have a SSL certificate that can be used, the switch to HTTPS can be done. 

.. note::
    This is supported from aprsd version 2.12
.. note::
    From version 3.0, HTTPS will be mandatory for login-sessions to other servers than localhost


Getting the certificate
^^^^^^^^^^^^^^^^^^^^^^^

A `certificate <https://en.wikipedia.org/wiki/Public_key_certificate>`_ is mainly a public key (cryptographic key) along with information about the identity of the owner of the certificate, that is signed by some `certification authority <https://en.wikipedia.org/wiki/Certificate_authority>`_ (CA). If we know and trust the CA (and its public key) we can check the digital signature of the certificate and we can trust that this certificate is authentic. When opening a connection to a HTTPS site a certificate of the server is first presented. If the client accepts it, we can use the public key of the server to securely establish an encrypted communication channel and authenticate the server (that it is really what it says it is). Web-browsers have installed a set of CA-certificates that it trusts. It is also possible for users to add or remove certificates and trust. We need a certificate for our *aprsd* server and there are mainly three ways to do it. We need to install it in the `Java keystore <https://en.wikipedia.org/wiki/Java_KeyStore>`_ format to be used by *aprsd*. 

**Alternative 1**: If the server runs on the same domain (for example `mydomain.org`) and machine as a frontend server which already have a certificate. We can use it. If this certificate is automatically installed by using a service like `Lets Encrypt <https://en.wikipedia.org/wiki/Let%27s_Encrypt>`_ and *certbot*, a certificate is placed at a certain location: You may check if the directory `/etc/letsencrypt/live/` exists. Then (as root) run the following command (assume that `mydomain.org` is the domain of your frontend server and the certificate):: 

    polaric-importcert-letsencrypt mydomain.org
    
It will install the certificate along with its private key in a keystore file available to *aprsd*. It will also generate and install the password for the keystore in the `server.ini` config file. 

**Alternative 2** If you have already a certificate created *manually*, either `self-signed <https://en.wikipedia.org/wiki/Self-signed_certificate>`_ or signed by a CA (typically by generating a CSR, sending it to a CA for signing), you can import it into the keystore. How to generate CSRs etc. is outside the scope of this document. Assume that you have the certificate and that it is stored in a file cert.pem and the private key is stored in a file privkey.pem it can be importted this way (make sure the private key is not password-protected and that the domain name of the certificate matches the real domain of your webserver)::

    polaric-importcert cert.pem privkey.pem
    

Activating HTTPS mode
^^^^^^^^^^^^^^^^^^^^^

When the certificate is imported, you can activate HTTPS mode by editing `/etc/polaric-aprsd/server.ini` and make sure that the ``httpserver.secure`` property is set to *true* and restart the server. 


Configuring the client
^^^^^^^^^^^^^^^^^^^^^^

You can tell the clients to use HTTPS mode by using this line in `/etc/polaric-webapp2/config.js`::
    
    SECURE(true)
    
    
    
Using the front-end webserver as a proxy
----------------------------------------

For permanent online services that are going to be available for users on the internet in general, using a separate port to access the backend REST API and websocket services may be impractical. A good workaround is to use the (Apache) front-end webserver as a proxy to the backend-server. If the Apache frontend and the backend is on the same machine, we don't need to configure the backend to be HTTPS and we may configure the firewall to block incoming traffic to port 8081. 

It will be somewhat specific to the server setup how to configure Apache to do the proxying but here are some of the config from *aprs.no*. The idea is that the `aprs.no/srv/` URL will be proxyed to `localhost:8081` where the backend is. Websocket (ws/wss) traffic is also proxyed::

    SSLProxyEngine On
    RequestHeader set Front-End-Https "On"

    ProxyPass /srv http://localhost:8081
    ProxyPass /srv/* http://localhost:8081
    ProxyPassReverse /srv http://localhost:8081
    SetEnv  proxy-nokeepalive 1
    ProxyTimeout 180 

    <Location "/ws/">
       ProxyPass ws://localhost:8081/
       ProxyPassReverse ws://localhost:8081/
    </Location>
    
.. note::
    This is meant to give you the idea and is not meant to be complete. I recommend to understand how this works before applying it to your own webserver. The internet is full of tutorials and discussions on how to do this.





