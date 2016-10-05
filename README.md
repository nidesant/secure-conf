# secure-conf
A collection of hardened configurations files for Web and SSL/TLS services such as nginx, apache, lighttpd, gpg, ssh etc.

## NGINX

There are a few things to take into account for the setup of nginx.conf. This setup is meant to be used as a best practice guide/template. In this example, the most modern cipher suites and ssl protocols are being used. However, this is prone to raise obvious comaptibility issues based on your own site setup and requirements. For a compaility matrix based on your nginx version, browser compaitiblity etc, see the second and third links pasted below under 'Useful Reading'. 

The first thing worth noting about this configuration is the use of PFS. All modern configurations should use ciphersuites that include ephemeral Diffie-Hellman key exchange. This will prevent an attacker from retoractively decrypting your past traffic/data as a new session key is generated at each new session. Adittionaly, other features include server_tokens set to off, automatic HTTP redirects, OWASP headers, a 4096 bit DH parameter, ssl stapling, and SSL/TLSv1.0 disabled. With server_token set to off, attackers will not be able to see what OS or version of NGINX you are running in response headers. You can prevent attackers from seeing nginx as well, but this requires compiling from source. In the first server block, all HTTP requests will be redirected to HTTPS, so the server will serve encrypted traffic by default. One of the most important OWASP headers being used is the HSTS header. Once a supported browser receives this header that browser will prevent any communications from being sent over HTTP to the specified domain and will instead send all communications over HTTPS. This must be set in your 443 server block. It is worth noting nginx add_header inheritance rules. NGINX configuration blocks inherit add_header directives from their enclosing blocks, so you just need to place the add_header directive in the top-level server block. There’s one important exception: if a block includes an add_header directive itself, it does not inherit headers from enclosing blocks, and you need to redeclare all add_header directives. A 4096 bit DHE parameter was created. This is because (for nginx 1.4.4 and above) Ephemeral Diffie-Hellman will use OpenSSL's defaults, which include a 1024-bit key for the key-exchange. This means DHE clients will use a weaker key-exchange than non-ephemeral DH clients. Creating a stronger DHE parameter can be found in the 'HOWTO' section. Lastly, ssl stapling being set to on allows the server to send its cached OCSP record during the TLS handshake, therefore bypassing the OCSP responder. This mechanism saves a roundtrip between the client and the OCSP responder of the certificate authority and prevents against DoS attacks on the CA's OCSP server. 

Useful reading:

https://www.eff.org/https-everywhere/deploying-https

https://mozilla.github.io/server-side-tls/ssl-config-generator/

https://wiki.mozilla.org/Security/Server_Side_TLS

**APACHE**
