## Generic development compile instructions
**WARNING**: this method uses the latest development code from the git repo. It that may be UNTESTED, you could LOSE COINS or corrupt your wallets. Back-up your wallet before using this code!

This produces the binary <tt>namecoind</tt> in <tt>namecoin/src</tt> directory.
```
 git clone https://github.com/namecoin/namecoin.git
 cd namecoin/src
 make -f makefile.unix namecoind
```
## Dependencies
Namecoind requires the following libraries:
* OpenSSL 1.0.1c
* Berkeley DB 4.8.30.NC
* Boost 1.37
* miniupnpc 1.6 (optional)

Namecoin-QT requires the following additional libraries:

* qt 4.8.
* protobuf 2.5.0
* libqrencode 3.2.0

You can find a more detailed list of dependencies for specific distributions and operating systems in their respective Bitcoin build-notes:
* [Linux](https://github.com/bitcoin/bitcoin/blob/master/doc/build-unix.md)
* [Mac OS X](https://github.com/bitcoin/bitcoin/blob/master/doc/build-osx.md)
* [Windows](https://github.com/bitcoin/bitcoin/blob/master/doc/build-msw.md)

#Windows

See http://dot-bit.org/BuildNamecoinQTFromSource

#Tips for building from source#

* standard Namecoin/Bitcoin build notes are in with source ~/namecoin/build-*.txt
* optionally building without upnp support
 make -f makefile.unix USE_UPNP=''
* Install libglib2.0-dev if you have the following error :
 /usr/bin/ld: cannot find -lgthread-2.0