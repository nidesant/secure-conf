# HOW TO 
Meant to provide users with instructions on generating ssl certs and keys. 

## Creating a Strong DH Parameter (Key) File

On your server, run the following commands in order:

    cd /etc/ssl/certs

    openssl dhparam -out dhparam.pem 4096 

Bit sizes can vary based on your cert and computational capabilities, but should be stronger than 1024 bits. 4096 bits can be computationally expensive; 2048 bit keys are acceptable. 

Lastly, tell nginx to use it for DHE key-exchange in your nginx configuration:

    ssl_dhparam /etc/ssl/certs/dhparam.pem;

## Generating GPG Key Pairs

To create your private and public key pair you issue the following command:

    gpg --gen-key
    
If you are using gnupg version 1.4.10 or newer, this will lead to a selection screen with these options:

    Please select what kind of key you want:
    (1) RSA and RSA (default)
    (2) DSA and Elgamal
    (3) DSA (sign only)
    (4) RSA (sign only)
    RSA keys may be between 1024 and 4096 bits long.
    What keysize do you want? (2048) 4096
    Requested keysize is 4096 bits
    Please specify how long the key should be valid.
      0 = key does not expire
      = key expires in n days
      w = key expires in n weeks
      m = key expires in n months
      y = key expires in n years
    Key is valid for? (0)
    Key does not expire at all
    Is this correct? (y/N) y
    
Selecting "RSA and RSA" for key will enable both encryption and signing. As for key size, it goes without saying that the longer the key size, the more secure it will be against attacks. However, computational capabilities may vary based on circumstance. It is recommended to have a key size of 4096 bits, but 2048 is acceptable. 

You will then be prompted to fill out your name, email address, comment, and to create a passphrase. 

    You need a user ID to identify your key; the software constructs the user ID
    from the Real Name, Comment and Email Address in this form:
      "Heinrich Heine (Der Dichter) &lt;heinrichh@duesseldorf.de&gt;"
    Real name: Foo Bar
    Email address: foo@bar.com
    Comment: My GnuPG keys
    You selected this USER-ID:
      "Foo Bar (My GnuPG keys) &lt;foo@bar.com&gt;"
    ...
    You need a Passphrase to protect your secret key.
    
It is obviously advised to create a strong passphrase. After this step, your key will be generated and you should see output similar to below:

    gpg: key E3DF66D2 marked as ultimately trusted
    public and secret key created and signed.
    pub   4096R/E3DF66D2 2012-01-18
      Key fingerprint = 95BD 8377 2644 DD4F 28B5  2C37 0F6E 4CA6 E3DF 66D2
    uid                  Foo Bar (My GnuPG keys) &lt;foo@bar.com&gt;
    sub   4096R/259EA73B 2012-01-18
    
The key ID of this secret key pair is the hexadecimal number next to pub 4090R ie. in this case E3DF66D2

Edit ~/.gnupg/gpg.conf and set the new secret key as the default key using the ID of the key pair:

    default-key E3DF66D2
    
If you ever need to view the ID of your key pair, it can be done with:

    gpg --fingerprint foo@bar.com
    
