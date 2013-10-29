# Debian Upgrading

> **system** Debian

Upgrading in Debian.

    $ vim ~/.bash_aliases

For the *stable* release

>     alias upgrade='sudo aptitude update && sudo aptitude safe-upgrade'

For *unstable*

>     alias upgrade='sudo apt-get update && sudo apt-get dist-upgrade'

Now run

    $ upgrade

