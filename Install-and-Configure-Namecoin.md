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

Populate a file "namecoin.conf" with at least these lines: 

```
rpcuser=your_user
rpcpassword=your_pass
rpcport=8336
daemon=1
```

Replace your_user and your_pass with any values of your choosing.

Also add this line if you want to solo mine: 

```
server=1
```
Now start namecoind again and you will get back to the command prompt. Namecoin runs in the background and will fetch the blockchain (which can take several hours).

To check on the progress of the download, try the namecoin getinfo command from the table below. 

### Important Commands
***

<table>
<th>Command</th><th>Description</th>
<tr>
<td>namecoind</td><td>Start Namecoin daemon</td>
</tr>
<tr>
<td>namecoind help</td><td>Show all available commands</td>
</tr>
<tr>
<td>namecoind getinfo</td><td> 	Show various information. blocks value should match the current block count ([152367])(http://explorer.dot-bit.org/stats/block_count.txt)</td>
</tr>
<tr>
<td>namecoind listreceivedbyaddress 0 true</td><td>List your Namecoin address on which you can receive Namecoins </td>
</tr>
<tr>
<td>namecoind getnewaddress</td><td>Create a new Namecoin address </td>
</tr>
<tr>
<td>namecoind name_new d/foobar</td><td>Reserve a domain name. See [this page](http://dot-bit.org/HowToRegisterAndConfigureBitDomains) for detailed instructions on how to register a domain.</td>
</tr>
</table>