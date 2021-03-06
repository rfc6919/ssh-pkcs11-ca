ssh-pkcs11-ca
=============

A tiny ssh CA whose CA key is on a PKCS #11 token
-------------------------------------------------

::

  usage: ssh-pkcs11-ca [-h] {authorized_keys,known_hosts,sign} ...

  positional arguments:
    {authorized_keys,known_hosts,sign}
      authorized_keys     print a suitable authorized_keys line for the current token
      known_hosts         print a suitable known_hosts line for the current token
      sign                produce a certificate on stdout for the public key provided on stdin

  optional arguments:
    -h, --help            show this help message and exit

when signing, take the same arguments as ssh-keygen does (except we take a single ``-O`` of comma-separated options rather than multiple ``-O`` of single options)::

  usage: ssh-pkcs11-ca sign [-h] [-I cert_identity] [-n principals] [-O options] [-V validity_interval] [-z serial_number]

  optional arguments:
    -h, --help            show this help message and exit
    -I cert_identity      identity logged by server on use of this certificate (default current username)
    -n principals         comma separated list of principals to include (default current username)
    -O options            comma separated list of options to include (default "clear,permit-pty")
    -V validity_interval  validity interval (default "-1m:+5m")
    -z serial_number      serial number


No configuration required, just run the script with a token plugged in.

Why would you use this?
-----------------------

If you keep your ssh keys on a token, and don't forward the agent connection, you can connect fine to Server A, or to Server B. But what happens when you want to connect from Server A *to* Server B?

::

               +----------+
          +--->| Server A |--+
          |    +----------+  |
  +--------+                 |
  | Laptop |                ???
  +--------+                 |
          |    +----------+  |
          +--->| Server B |<-+
               +----------+

You could create a keypair on Server A and add the pubkey to Server B, and then remember to remove the pubkey when you're done. Or just use this tool to create a certificate with 5-minute validity.

This is only useful for personal stuff where the individual token holder is the ultimate trust root. If you want ssh certificates for more than one person, this isn't the CA for you.
