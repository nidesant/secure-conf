# secure-conf
A collection of hardened configurations files for Web and SSL/TLS services such as nginx, apache, lighttpd, gpg, ssh etc.

## NGINX

There are a few things to take into account for the setup of nginx. This setup is meant to be used as a best practice guide/template. In this example, the most modern cipher suites and ssl protocols are being used. However, this is prone to raise obvious comaptibility issues based on your own site setup and requirements. For a ciphersuite compaility matrix based on your nginx version, browser etc, see the second and third links pasted below under 'Useful Reading'. 

The first thing worth noting about this configuration is the use of PFS. All modern configurations should use ciphersuites that include ephemeral Diffie-Hellman key exchange. This will prevent an attacker from retoractively decrypting your past traffic/data as a new session key is generated at each new session. Adittionaly, other features include server_tokens set to off, automatic HTTP redirects, OWASP headers, a 4096 bit DH parameter, ssl stapling, and SSL/TLSv1.0 disabled. 

With server_tokens set to off, attackers will not be able to see the server signature in response headers. You can prevent attackers from seeing nginx as well, but this requires compiling from source. 

In the first server block, all HTTP requests will be redirected to HTTPS, so the server will serve encrypted traffic by default. 

One of the most important OWASP headers being used is the HSTS header. Once a supported browser receives this header, that browser will prevent any communications from being sent over HTTP to the specified domain and will instead send all communications over HTTPS. This must be set in your ssl server block. It is worth noting nginx add_header inheritance rules. NGINX configuration blocks inherit add_header directives from their enclosing blocks, so you just need to place the add_header directive in the top-level server block. There’s one important exception: if a block includes an add_header directive itself, it does not inherit headers from enclosing blocks, and you need to redeclare all add_header directives. 

A 4096 bit DHE parameter was created. This is because (for nginx 1.4.4 and above) Ephemeral Diffie-Hellman will use OpenSSL's defaults, which include a 1024-bit key for the key-exchange. This means DHE clients will use a weaker key-exchange than non-ephemeral DH clients. Creating a stronger DH parameter can be found in the 'HOWTO' section. 

Lastly, ssl stapling being set to on allows the server to send its cached OCSP record during the TLS handshake, therefore bypassing the OCSP responder. This mechanism saves a roundtrip between the client and the OCSP responder of the certificate authority and prevents against DoS attacks on the CA's OCSP server. 

Useful reading:

https://www.eff.org/https-everywhere/deploying-https

https://mozilla.github.io/server-side-tls/ssl-config-generator/

https://wiki.mozilla.org/Security/Server_Side_TLS

## APACHE

The apache confugration is very similar to the setup of nginx, and is meant to be used as a best practice guide/template. This configuration was devleoped against CIS (Center for Internet Security) benchmarks for the apache server (there is a link to the benchmark document in the 'Useful reading' section). In this example, the most modern cipher suites and ssl protocols are being used for the most recent versions of apache (2.4.x). This configuration also makes use of PFS-only ciphersuites. Adittionaly, other features include server_tokens/signature set to off, automatic HTTP redirects, ssl stapling, OWASP headers, and all versions of SSL disabled. 

There are more features worth mentioning that have been added to the apache configration that are not configured in nginx. These features and their funcinonalities are explained in brief below:

Restrict options directive for / and other directives - The	Options directive	for	the	root	OS	level	is	used	to	create	a default	minimal	options	policy	that	allows	only	the	minimal	options	at	the	root	directory	level.	

Limit http request methods - The HTTP	1.1	protocol supports	several	request	methods	which are	rarely used	and	potentially	high risk.	For	example, methods such as PUT and DELETE are rarely used and	should be disabled in keeping	with the	primary	security principal of minimize features and options.	

Disable http trace method - The HTTP 1.1 protocol requires support for the TRACE request method which reflects the request back as a response and was intended for diagnostics purposes. The TRACE method is not needed and is easily subjected to abuse and should be disabled.

Rewrite	rule	to	the	global	server	level	configuration	to	disallow	other	protocol	versions - Many malicious automated programs, vulnerability scanners and fingerprinting tools will send abnormal HTTP protocol versions to see how the web server responds. These requests are usually part of the attacker's enumeration process and therefore it is important that we respond by denying these requests.

Restrict	access	to	inappropriate	file	extensions - There are many files that are often left within the web server document root that could provide an attacker with sensitive information. Most often these files are mistakenly left behind after installation, trouble-shooting, or backing up files before editing.

Deny IP based requests - A common malware propagation and automated network scanning technique is to use IP addresses rather than host names for web requests, since it's much simpler to automate. By denying IP based web requests, these automated techniques will be denied access to the website. 

Set	Timeout	Limits for	Request	Headers and request body - Setting a request header timeout is vital for mitigating Denial of Service attacks based on slow requests. The slow request attacks are particularly lethal and relative easy to perform, because they require very little bandwidth and can easily be done through anonymous proxies. 

Disable modules - There are many apache modules that should be minimized in a live system. Typical modules to disable are the Info module, User Directories module, Proxy module, Autoindex module, Status module, and the WebDAV module. 

Lastly, this configuration is meant to demonstrate apache's modularity and flexibility. Many directives were defined within various configuration files. Instead of clogging the global configuration (apache2.conf), many of the features described above are defined within conf.d/security. Additional directives are also defined within sites-enabled/000-default. These directives will serve as the default directives for directives not explicitly defined within specific virtual host settings. For example, you can define a Webmaster email address and not define individual email addresses for each virtual host. 

Useful Reading:

https://benchmarks.cisecurity.org/tools2/apache/CIS_Apache_HTTP_Server_2.2_Benchmark_v3.4.0.pdf
