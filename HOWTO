# HOW TO 
Meant to provide users with instructions on generating ssl certs and keys. 

### Creating a Strong DH Parameter (Key) File

cd /etc/ssl/certs
openssl dhparam -out dhparam.pem 4096

Lastly, tell nginx to use it for DHE key-exchange:
ssl_dhparam /etc/ssl/certs/dhparam.pem;

