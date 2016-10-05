# HOW TO 
Meant to provide users with instructions on generating ssl certs and keys. 

## Creating a Strong DH Parameter (Key) File

On your server, run the following commands in order:

    cd /etc/ssl/certs

    openssl dhparam -out dhparam.pem 4096 

Bit sizes can vary based on your cert and computational capabilities, but should be stronger than 1024 bits.

Lastly, tell nginx to use it for DHE key-exchange in your nginx configuration:

    ssl_dhparam /etc/ssl/certs/dhparam.pem;

