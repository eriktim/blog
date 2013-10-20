# Sopcast

This micro guide (inspired by [[2]][blog]) describes how to run Sopcast on Debian using the command-line.

> **system** Debian Sid  
> **versions** VLC 2.0.8, Sopcast 3.2.6  

## Installation

First install VLC which is used for playing the stream

    $ sudo apt-get install vlc

Download the required Sopcast files [[1]][sopcast], unzip them and add Sopcast to the system's binaries

    $ wget http://download.easetuner.com/download/sp-auth.tgz
    $ tar -xvf sp-auth.tgz
    $ sudo cp sp-auth/sp-sc-auth /usr/local/bin/
    $ rm sp-auth.tgz

Test whether Sopcast is working by running

    $ sp-sc-auth

### Errors

If the following error occures

    sp-sc-auth: error while loading shared libraries:
    libstdc++.so.5: cannot open shared object file: No such file or directory

this can be fixed by running

    $ wget http://www.sopcast.com/download/libstdcpp5.tgz
    $ tar -xvf libstdcpp5.tgz
    $ sudo cp -a libstdcpp5/* /usr/lib/
    $ rm libstdcpp5.tgz

## Usage

Run Sopcast

    sp-sc-auth sop://broker.sopcast.com:3912/<channel> 3908 8908 > /dev/null

View the Sopcast stream with VLC

    vlc http://localhost:8908/tv.asf > /dev/null

## References

1. <http://www.sopcast.org/download/linux.html>
2. <http://fedellandogz.wordpress.com/2012/11/26/howto-install-sopcast-in-ubuntu-debian/>

[sopcast]: http://www.sopcast.org/download/linux.html 
[blog]: http://fedellandogz.wordpress.com/2012/11/26/howto-install-sopcast-in-ubuntu-debian/ 

