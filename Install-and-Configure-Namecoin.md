### Quick start on Linux
***
This is a quick way to get namecoind running on your Linux machine.
Download & extract the archive and create initial configuration files by executing namecoind:


```Shell
wget -O /tmp/namecoin.tgz http://dot-bit.org/files/namecoin_linux`getconf LONG_BIT`.tgz
tar xfz /tmp/namecoin.tgz -C ~/usr/local/bin/
```

Fill in the configuration:

```Shell
mkdir ~/.namecoin && \
echo "rpcuser=`whoami` \n\
rpcpassword=`openssl rand -hex 20` \n\
rpcport=8336" > ~/.namecoin/namecoin.conf
```

And finally start namecoind:

`~/namecoind -daemon`

If you want to skip the -deamon flag you can run:

`echo "daemon=1" >> ~/.namecoin/namecoin.conf`

### Download
***
[Download](http://dot-bit.org) namecoin from the homepage and extract the archive. 

### Configuration
***
Start namecoind to create the config folder: 

<table>
<th>Executing Namecoind<th>
<tr><td>Windows</td><td>Open a command prompt, switch to the directory of the extracted file and type
namecoind</td>
</tr>
<tr>
<td>Linux</td><td>Open a terminal (Ctrl+Alt+T in many desktop environments), switch to the directory of the extracted file and type
./namecoind</td>
</tr>
</table>

It will tell you where to create the "namecoin.conf" file.

```Shell
Warning: To use namecoind, you must set rpcpassword=<password>
in the configuration file: /home/user/.namecoin/namecoin.conf
If the file does not exist, create it with owner-readable-only file permissions.
```
<table>
<th>Configuration File Location</th>
<tr>
<td>Windows</td><td>%APPDATA%\namecoin</td>
</tr>
<tr>
<td>Linux</td><td>~/.namecoin</td>
</tr>
</table>