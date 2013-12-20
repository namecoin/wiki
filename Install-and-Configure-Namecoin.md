## Quick start on Linux
***
This is a quick way to get namecoind running on your Linux machine.
Download & extract the archive and create initial configuration files by executing namecoind:


```Shell
wget -O /tmp/namecoin.tgz http://dot-bit.org/files/namecoin_linux`getconf LONG_BIT`.tgz
tar xfz /tmp/namecoin.tgz -C ~/usr/local/bin/
```

Fill in the configuration:

```
mkdir ~/.namecoin && \
echo "rpcuser=`whoami` \n\
rpcpassword=`openssl rand -hex 20` \n\
rpcport=8336" > ~/.namecoin/namecoin.conf
```

And finally start namecoind:

`~/namecoind -daemon`

If you want to skip the -deamon flag you can run:

`echo "daemon=1" >> ~/.namecoin/namecoin.conf`
