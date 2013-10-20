# Debian Package Downgrading

If an upgraded package appears to be broken, here is how to downgrade to any previous version.

> **system** Debian Sid  

    $ cd /var/cache/apt/archives
    $ ls * | grep <package-name>
    $ sudo dpkg -i <package-name>_<version>.deb

