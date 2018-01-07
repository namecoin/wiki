# Register a domain with a registrar

* http://register.dot-bit.org — payments accepted: BTC (Bitcoins)
* http://getbit.eu — payments accepted: BTC (Bitcoins)
* http://dotbit.me — payments accepted: BTC (Bitcoins), NMC (Namecoins) or LTC (Litecoins)
* https://domaincoin.net — payments accepted: BTC (Bitcoins)
* https://criptodomini.com - payments accepted: BTC (Bitcoins), NMC (Namecoins) LTC (Litecoins) and Paypal

Advantages:
* No need to run your own node
* No need to extend the domain yourself
* No need to deal with Namecoins directly
Disadvantages:
* Have to trust the registrar
* You don't own the domain

# Register a domain with namecoin
## How much does it cost
You can register a .bit domain by buying it with namecoins with the namecoin software. You will be your own registrar by using the namecoin software. The fee consists of two parts: the registration fee plus the transaction fee (as for every transaction).
<table>
<tr><th>Command</th><th>Registration Cost</th><th>Fees</th><th>Summary</th><th>Notes</th></tr>
<tr><td>name_new</td><td>0.01NMC</td><td>Standard*</td><td>pre-order a domain, fixed cost</td><td><strong>You still don't own the domain!</strong></td></tr>
<tr><td>name_firstupdate</td><td>0NMC (<a href="http://dot-bit.org/tools/domainCost.php">Price Calculator</a>)</td><td>Standard*</td><td>register a domain, it becomes public, variable cost</td><td>You own the domain!</td></tr>
<tr><td>name_update</td><td>0.00NMC</td><td>Standard*</td><td>domain is updated, transfered or renewed</td><td>Reset the expire time to the max</td></tr>
</table>
\* Standard fees are 0.005NMC (same rules as bitcoin for tx with no fees).
## 1. Install the namecoin software
[Install and configure namecoin](https://github.com/namecoin/namecoin/wiki/Install-and-Configure-Namecoin)

## 2. Pre-order a domain name
<name> is your domain without .bit, in lowercase. For internationalized domain names, the [IDNA](http://en.wikipedia.org/wiki/IDNA) ASCII standard is used, and direct Unicode entries are not supported.
Use this online tool to check and convert your domain : http://dot-bit.org/tools/domainSearch.php
Or check if your domain if free with the command line (the command line don't do the conversion) :
> ./namecoind name_new d/<name>

Start the namecoin software and use the following command :
> ./namecoind name_new d/<name>

The output will looks like this :
> [
>   "0e0e03510b0b0b7dbba6e301e519693f68062121b29f3cd3a6652c238360d0d0",
>   "9f213ff4a582fd65"
> ]

This will reserve a name but not make it visible yet (You still don't own the domain!). Make a note of the short hex number (<rand>). You will need it for the next step. (Do not shut down namecoind or you will also have to supply the longer hex code just below, just after the short one.) Wait at least 12 blocks, which is generally between 2 hours and 2 days (depending on how active the network is), then update your domain.

If you lose the random number, you can just issue name_new again. You still need to wait 12 blocks before updating your domain.

## 3. Register your domain to show your web pages
> ./namecoind name_firstupdate d/<name> <rand> '<json-value>'

where <rand> is the shorter value you got from the previous command
where <json-value> is a JSON-encoded string that contains your nameserver information. Note that your JSON must be enclosed in quotes and escaped properly. See below for examples on how to properly format this string.

If you see this error message:

> error: {"code":-1,"message":"previous tx used a different random value"}

try adding the long hex number

> ./namecoind name_firstupdate d/<name> <rand> <longhex> '<json-value>'

## 4. Other updates
> ./namecoind name_update d/<name> '<json-value>'

Each name_update reset the expire time to it's maximum value. Namecoin software will do this automatically in a future release.

## 5. Configure your web server
Add your domain on your web hosting server, like you do it for other domains.
For example, to host a yourdomain.bit you would add the following virtual host entry to your Apache configuration file.

`<VirtualHost *:80>
       DocumentRoot /www/yourdomain.bit
       ServerName www.yourdomain.bit
</VirtualHost>`

Most shared hosts (like Dreamhost, Hostgator, Bluehost, etc) and control panels (like CPanel) have error checking mechanisms that prevent non-traditional TLD's from being added. The best you can do is try to add your .bit domain using the normal domain configuration interface they provide and contact their technical support if this fails.

# How to configure your domain

Adapted from : https://github.com/vinced/namecoin/blob/master/README.md

Values in the name_update command are JSON encoded. Here is a syntax validator that checks your json-value : Check json-value syntax

=> use it to get proper escaping for windows.

## Delegate your domain and subdomains to DNS servers:
Recommended:
* {"ns": ["ns0.web-sweet-web.net", "ns1.web-sweet-web.net", "ns0.xname.org"]}

Avoid ip if possible :
* {"ns": ["1.2.3.4", "1.2.3.5"]}

=> Full zone control (standard way of managing domains). IP or hostname allowed.

Step required:
* Add example.bit in the same interface where you added example.com

If you can only add a domain by registering it, you can't use this method. You may use another web hosting provider.

## Map all hosts in the domain to one IP address:
* {"ip":"1.2.3.4", "map": {"*": {"ip":"1.2.3.4"}}}

Example:
`./namecoind name_update d/<name> '{"ip":"1.2.3.4", "map": {"*": {"ip":"1.2.3.4"}}}'`

=> No custom subdomain : domain.bit and *.domain.bit point to 1.2.3.4

Step required:
* Add example.bit in the same interface where you added example.com

If your web hosting provider don't accept unknown TLDs (.bit for example), you must use another provider.

## Map hosts in the domain to several IP addresses:
* {"ip":"1.2.3.4", "map": {"demo": {"ip":"1.2.3.5"}}}

Example:
`./namecoind name_update d/<name> '{"ip":"1.2.3.4", "map": {"demo": {"ip":"1.2.3.5"}}}'`

=> 1 custom subdomain : demo.domain.bit points to 1.2.3.5 and, domain.bit points to 1.2.3.4

## Do a translation step before delegation:
* {"translate": "bitcoin.org", "ns": ["1.2.3.4", "1.2.3.5"]}

in which case, foo.example.bit will be translated to foo.bitcoin.org before the DNS server will reply.

Now compatible with DNS servers using a recent NamecoinTobind.
