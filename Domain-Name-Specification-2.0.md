== Goals of this Specification ==
* Standardized support for most, if not all<sup>1</sup>, DNS record types.
* Ability to provide alternative mappings for non-IP networks, to be processed by proxy servers.
* Allowing delegation of the domain or sub-domains to master NS servers, while still providing support for non-IP networks.
* Enabling hierarchical structures within Namecoin.
* Establish a standard for future extensions.

<sup>1</sup> <small>Some standard resource records, like SOA, can be derived from other records, and others may not be applicable in a distributed naming service context. Also see [[#TODO]].</small>


== Name field ==

Name is a lowercase string, which begins with <tt>d/</tt>, followed by the domain name. Namecoin enabled resolvers will append the default TLD (<tt>.bit</tt>) to the name specified here.

; Example
: <tt>d/example</tt>

The [http://en.wikipedia.org/wiki/IDNA IDNA] standard encoding is used for internationalized domain names. This means that Unicode names need to be converted to ASCII compatible encoding according to IDNA, before registration. This can be done using the <tt>idn</tt> command-line tool:
<pre>
$ idn -p Nameprep ŝtelo
xn--telo-u5a
</pre>

Also, only valid domain names are allowed, which means characters other than lowercase letters, numerics and dash are not allowed, as well as names longer than 63 letters and names starting with a dash.

''(Note: However, since this is a measure against compatibility issues with DNS, names consisting of all numeric values and underscore can be discussed.)''


== Value field ==

Value is a UTF&ndash;8 encoded [http://en.wikipedia.org/wiki/JSON#Data_types.2C_syntax_and_example JSON Object] with a maximum size of 1023 bytes, where all entries are case-sensitive.

The JSON Object (the ''domain configuration'') is an associative array of configuration options of the domain in context. Since sub-domains are also configured via a ''domain configuration'', we will use the word ''domain'' to signify the item that is being configured, even though it may be a sub-domain in the context of the value field.

<dl>
<dt>Example
<dd><pre>
{
    "ip"      : "192.168.1.1",
    "ip6"     : "2001:4860:0:1001::68",
    "tor"     : "eqt5g4fuenphqinx.onion",
    "email"   : "hostmaster@example.bit",
    "info"    : "Example & Sons Co.",
    "service" : [ ["smtp", "tcp", 10, 0, 25, "mail"] ]
    "tls": {
        "tcp": {
            443: [[1, "660008F91C07DCF9058CDD5AD2BAF6CC9EAE0F912B8B54744CB7643D7621B787", 1]]
        } {
            25: [[1, "660008F91C07DCF9058CDD5AD2BAF6CC9EAE0F912B8B54744CB7643D7621B787", 1]]
        }
    }
    "map":
    {
        "www" : { "alias": "" },
        "ftp" : { "ip": ["10.2.3.4", "10.4.3.2"] },
        "mail": { "ns": ["ns1.host.net", "ns12.host.net"] }
    }
}
</pre>
</dl>

Below is a list of possible entries. Matching DNS resource record types are also listed. 

{| cellspacing="8"
!  align="left" | Type
!  align="left" | Description
!  align="left" | DNS
!  align="left" | Example
|-
|  align="left" | <tt>service</tt>
|  align="left" | Used to identify hosts that support particular services as per DNS <tt>SRV</tt> records. ''(see [[#Service records]])''
| <tt>SRV</tt>
|  align="left" | <tt>&quot;service&quot;: [</tt><br /><tt>[&quot;imap&quot;, &quot;tcp&quot;, 0, 0, 143, &quot;mail.host.com.&quot;]</tt><br /><tt>]</tt>
|-
|  align="left" | <tt>ip</tt>
|  align="left" | IPv4 addresses. ''(see [[#Addresses]])''
| <tt>A</tt>
|  align="left" | <tt>&quot;ip&quot;: [&quot;192.168.1.1&quot;, &quot;192.168.7.1&quot;]</tt>
|-
|  align="left" | <tt>ip6</tt>
|  align="left" | IPv6 addresses.
| <tt>AAAA</tt>
|  align="left" | <tt>&quot;ip6&quot;: [&quot;2001:4860:0:1001::68&quot;]</tt>
|-
|  align="left" | <tt>tor</tt>
|  align="left" | Tor hidden service address.
| -
|  align="left" | <tt>&quot;tor&quot;: &quot;eqt5g4fuenphqinx.onion&quot;</tt>
|-
|  align="left" | <tt>i2p</tt>
|  align="left" | Eepsite information. At least one hint is required.
| -
|  align="left" | <tt>&quot;i2p&quot;: {</tt><br /><tt>&quot;destination&quot;: &quot;XaZscx...0jGAAAA&quot;</tt><br /><tt>&quot;name&quot;       : &quot;example.i2p&quot;</tt><br /><tt>&quot;b32&quot;        : &quot;ukeu...nkdq.b32.i2p&quot;</tt><br /><tt>}</tt>
|-
|  align="left" | <tt>freenet</tt>
|  align="left" | Freesite Key.
| -
|  align="left" | <tt>&quot;freenet&quot;: &quot;USK@0I8g...xbZ4,AQACAAE/Example/42/&quot;</tt>
|-
|  align="left" | <tt>alias</tt>
|  align="left" | Specifies that this name is an alias of the given String, which can either be one of the sub-domain names in context or an absolute domain name. Absolute domain names are signified by an added dot (<tt>.</tt>) in the end. ''(see [[#Domain references]])''
| <tt>CNAME</tt>
|  align="left" | <tt>&quot;alias&quot;: &quot;&quot;</tt>
|-  class="even"
|  align="left" | <tt>translate</tt>
|  align="left" | Specifies that all subdomains of this name are translated to the given String before lookup. As with <tt>alias</tt>, absolute domain names end with a dot (<tt>.</tt>). Ex: <tt>&quot;subdomain.test.bit&quot;</tt> could be translated to <tt>&quot;subdomain.otherhost.bit&quot;</tt>.
| <tt>DNAME</tt>
|  align="left" | <tt>&quot;translate&quot;: &quot;otherhost.bit.&quot;</tt>
|-
|  align="left" | <tt>email</tt>
|  align="left" | Hostmaster e-mail address. ''(This is recommended at the domain level and will be inserted into the SOA record of DNS bridges; see [[#Implementation]].)''
| <tt>SOA</tt>, <tt>RP</tt>
|  align="left" | <tt>&quot;email&quot;: &quot;hostmaster@example.bit&quot;</tt>
|-
|  align="left" | <tt>loc</tt>
|  align="left" | Geographic location information.
| <tt>LOC</tt>
|  align="left" | <tt>&quot;loc&quot;: &quot;51 30 12.240 N 0 7 40.254 W 0m&quot;</tt>
|-
|  align="left" | <tt>info</tt>
|  align="left" | A JSON value reserved for registrant information. Recommended at the domain level. ''(see [[#Whois]])''
| -
|  align="left" | <tt>&quot;info&quot;: &quot;buyme@example.bit&quot;</tt>
|-
|  align="left" | <tt>ns</tt>
|  align="left" | Array of master nameservers of the configured domain, which can be either IPs or absolute domain names. Note that this delegates all IP related responsibility of this domain and its sub-domains to the master server, effectively bypassing other settings (e.g. <tt>ip</tt>). ''(see [[#Implementation]])''
| <tt>NS</tt>
|  align="left" | <tt>&quot;ns&quot;: ["ns.myserver.net", "192.168.3.4"]</tt>
|-
|  align="left" | <tt>delegate</tt>
|  align="left" | Delegates control of this domain to the given Namecoin name, or a sub-domain entry defined within that name. All other entries are ignored. ''(see [[#Importing and delegation]])''
| [[#Importing and delegation|*]]
|  align="left" | <tt>&quot;delegate&quot;: [&quot;s/example74845&quot;]</tt>
|-
|  align="left" | <tt>import</tt>
|  align="left" | Imports specified entries from Namecoin names and merges with the current one. ''May be implemented in the future to meet demands about extra space and data sharing.''
| [[#Importing and delegation|*]]
|  align="left" | <tt>"import": [ ["d/example", "www"] ]</tt>
|-
|  align="left" | <tt>map</tt>
|  align="left" | Maps sub-domains to their respective configurations. (see [[#Sub-domains]])
| -
|  align="left" | <tt>"map": {<br />"www": { "alias" : "www.example.com." },<br />"www2": { "delegate": ["d/example", "www"] }<br />}</tt>
|-
|  align="left" | <tt><i>deprecated :</i><br />fingerprint<br />See "tls" below.</tt>
|  align="left" | Specifies one or more certificate fingerprints. (see [[#TLS support]])
| -
|  align="left" | <tt>"fingerprint": [ "15:91:52:97:10:88:CD:44:9C:F7:23:81:78:C3:50:3B:09:20:56:2A", "63:08:84:E2:79:CB:11:07:F1:FB:8A:6B:11:A6:4D:1B:14:76:3F:8E" ]</tt>
|-
|  align="left" | <tt>tls</tt>
|  align="left" | Specifies one or more certificate fingerprints for specific protocols and ports. Attempts to follow the DANE protocol closely. Adds includeSubdomains.<br />Complete protocol specification http://dot-bit.org/forum/viewtopic.php?f=5&t=1137
| -
|  align="left" | <tt><pre>"tls": {
    "tcp": {
        443: [[1, "660008F91C07DCF9058CDD5AD2BAF6CC9EAE0F912B8B54744CB7643D7621B787", 1]]
    } {
        25: [[1, "660008F91C07DCF9058CDD5AD2BAF6CC9EAE0F912B8B54744CB7643D7621B787", 1]]
    }
}</pre></tt>
|}



=== Addresses ===

Multiple addresses per domain can be specified using String Arrays. This is currently proposed for only IPv4 and IPv6 but can be extended to other addresses in the future. Multiple addresses will be used for round-robin load distribution.

; Example with multiple IPs
: <tt>"ip6": ["2001:4860:0:1001::68", "2001:4860:0:1001::69", "2001:4860:0:1001::70"]</tt>

; Examples with a single IP
: <tt>"ip6": "2001:4860:0:1001::68"</tt>
: <tt>"ip6": ["2001:4860:0:1001::68"]</tt>


=== Domain references ===

<tt>service</tt>, <tt>alias</tt> and <tt>translate</tt> entries accept references to other domains.

To reference domains in the current context, enter the referenced name as-is, or in the case of inner sub-domains, insert dots (<tt>.</tt>) between names.

; Examples
: <tt>"www"</tt>
: <tt>"ftp.eu"</tt>

An empty String (<tt>&quot;&quot;</tt>) signifies the currently configured domain. In the below example, <tt>www.us.<domain></tt> is an alias for <tt>us.<domain></tt> (and not <tt><domain></tt> itself).

<pre>
{ 
    "map": { 
        "us": {
            "map": { "www": { "alias": "" } }
        } 
    }
}
</pre>

To reference absolute domain names, add a dot (<tt>.</tt>) to the end of the String to signify the root domain.

; Examples
: <tt>"google.com."</tt>
: <tt>"ns.example.bit."</tt>

The <tt>@</tt> character is also an absolute reference, and is converted to the topmost domain of the Namecoin value field that is being configured. For standard domains in the <tt>d/</tt> namespace, that is the domain name (see [[#Importing and delegation]]).


=== Sub-domains ===

<tt>"map"</tt> Object is an associative array mapping sub-domains to their configurations. Configuration format is the the same as the value field itself, making moving, copying and importing domains easier.

There are two special sub-domain specifiers, <tt>""</tt> and <tt>"*"</tt>, which are respectively the [[#Domain references|reference to current domain]] and the wildcard record. Wildcard matches all non-existent sub-domain names in the current context.

If a map record contains a "" record, but the root specifies a corrosponding record (such as an ip/ip6/onion record), than the record in the root takes precedence.

<dl>
<dt>Sample entry for <tt>d/domain</tt>
<dd><pre>
{
    "ip" : "8.8.8.8",
    "map":
    {
        "us":
        {
            "ip" : "1.2.3.4",
            "map": { "www": { "alias": "" } }
        },
        "eu":
        {
            "map": { "www": { "alias": "us.@" } }
        },
        "*": { "alias": "" }
    }
}
</pre></dl>

In the above example, <tt>us.domain.bit</tt>, <tt>www.us.domain.bit</tt> and <tt>www.eu.domain.bit</tt> resolve to the IP <tt>1.2.3.4</tt>, while <tt>eu.domain.bit</tt> doesn't have an assigned IP.

Also, the wildcard implies that, for instance, <tt>www.domain.bit</tt> is an alias for <tt>domain.bit</tt> and resolves to <tt>8.8.8.8</tt>, but it doesn't imply that <tt>nonexistent.us.domain.bit</tt> will resolve.

=== Importing and delegation ===

Delegation of domain configuration is made possible by the <tt>delegate</tt> record type. This can be used to extend the space for domain data, better organization, handing over management responsibilities, sharing data between domains, etc.

''Note: The <tt>s/</tt> application specifier is proposed to distinguish non-resolvable domain data from domain names. Other specifiers, like <tt>dd/</tt> for "domain data" can also be considered.''

; Examples
: <tt>"delegate": ["s/example001"]</tt>
: <tt>"delegate": ["d/example", "ftp"]</tt>
: <tt>"delegate": ["s/exampledata99", ""]</tt>

The referenced Namecoin entry does not have to be in the domain namespace (<tt>"d/"</tt>), and will be treated as a domain configuration JSON Object. The <tt>s/</tt> application specifier is '''proposed''' to distinguish non-resolvable domain data from domain names.

Sub-domains can be referenced through a second item in the array, using the sub-domain reference notation. So, <tt>["s/example001"]</tt> and <tt>["s/example001", ""]</tt> refer to the same data.

Such delegations override ''all'' other entries, including <tt>ns</tt> and <tt>info</tt>. Since this renders other entries unnecessary, a second notation is '''proposed''' at the sub-domain mapping level:

<pre>
"map": {
    "ftp": ["s/somerandomname"],
    "www": ["d/otherdomain", "www"]
}
</pre>

''Note: If the above proposal is accepted, the <tt>delegate</tt> directive would get precedence in the case of <tt>""</tt> sub-domain.''

''Warning: Details about the below <tt>import</tt> directive aren't clear yet.''

The <tt>import</tt> directive is provided to simply merge domains from different Namecoin records with the current one. It's an array of references to Namecoin domain entries, which have the same notation as <tt>delegate</tt> references. However, instead of passing the control to the referred configurations, data is imported to the current context and then overridden by explicitly provided entries.

<dl>
<dt>d/example
<dd><pre>
{
    "import": [ ["s/shareddata", "www"], ["s/shareddata", "ftp"] ],
    "ip"    : "10.2.3.4",
    "map"   : { "ftp" : { "email": "example@mail.bit" } }
}
</pre>
<dt>s/shareddata
<dd><pre>
{
    "ip" : "10.0.0.1",
    "map": { 
        "www" : { "alias": "" },
        "ftp" : { "ip": "10.0.1.2", "email": "shared@mail.bit" }
    }
}
</pre>
</dl>

Here, data from <tt>d/shareddata</tt> is imported from within <tt>d/example</tt>. Since data is imported as-is, <tt>www.example.bit</tt> becomes a reference to <tt>example.bit</tt> and resolves to <tt>10.2.3.4</tt> and not <tt>10.0.0.1</tt>. On the other hand, <tt>email</tt> field of the imported <tt>ftp</tt> subdomain is modified.

Of course, instead of importing specific subdomains one by one, <tt>"import": [ ["s/shareddata"] ]</tt> could be used in the above example.

=== Service records ===

TODO (see [http://en.wikipedia.org/wiki/SRV_record <tt>SRV</tt> records])

DNS <tt>MX</tt> records need to be derived from the <tt>service</tt> records for SMTP.

=== TLS support ===

The traditional TLS trust model is very centralized. The ability to create a certificate for your web site is concentrated in the hands of a select few governments and corporations. By having your site support TLS, you are putting yourself at their mercy. This goes squarely against the principles of decentralization and distributed trust at the core of the Namecoin project.

Fortunately, it is possible to use TLS without relying on a central certificate authority list. The mechanism is defined as follow:
<ul>
<li>Generate a self-signed certificate for your Namecoin domain, or a sub-domain. Wildcards are okay.
<li>Add a <tt>fingerprint</tt> field in your Namecoin record, containing the fingerprint for this certificate. If targeting a sub-domain, place it in the proper <tt>map</tt> field.
</ul>
On the user side, Namecoin client software that implement this specification will intercept TLS requests to your domain/sub-domain. The certificate returned by your server will be matched against your Namecoin record.<br>
If the certificate is self-signed, the certificate common name matches the requested hostame, the certificate time range is valid and the certificate fingerprint matches one of the fingerprint found in the record subset matching the given hostname, the client software will allow the TLS connection to proceed, and make it appear valid and trusted to the user.

That later part is tricky, as most TLS clients are designed to work with a centralized trust model. It is recommended that Namecoin client software generate a unique self-signed certificate and install it as a trusted root Certificate Authority in the appropriate TLS clients, such as web browsers. Once that initial setup is done, Namecoin client software can use that root certificate to generate TLS certificates that browsers will accept. Namecoin client software should cache such generated certificates and reuse them as needed.

Note that the <tt>fingerprint</tt> accepts an array of fingerprint. This allows a site owner to update their TLS certificate without downtime. In such a scenario, a site owner would generate a new certificate, update his Namecoin record to hold both the old and new fingerprint, and wait until the record has safely propagated. Then the site could start using the new certificate, and the old fingerprint could be safely removed from the record.

<i>This isn't the first attempt to use TLS certificate fingerprint in a DNS-like system to replace the traditional CA model. In fact Chrome 14 supports certificate fingerprints in DNSSEC-signed responses (http://www.imperialviolet.org/2011/06/16/dnssecchrome.html ), and there are talks in the Firefox dev team to implement something similar (https://wiki.mozilla.org/Security/DNSSEC-TLS )<br>
This opens the possibility that there might be a smoother way in the future to implement this in Namecoin that wouldn't require users to add a strange CA to their browsers.<br>
I'd be interested in someone knowledgeable in DNSSEC commenting on this, and the odds of Namecoin being able to serve DNSSEC records that end-user applications might accept.</i> 

--

<span style="font-size: x-small">(LS: Someone knowledgeable in DNSSEC... That'll be hard to find around here. I've done my best to work with it, and it's just like 'authoritative' SSL. You pretty much have to cram a bunch of authorities down someones throat to bootstrap it, then eventually decorate it with some gold, green, or color du jour. While I don't consider myself an expert in it at all, I've done enough work with it to see it as the same thing. Namecoin is definitely the appropriate way to manifest the illusion DNSSEC is aiming to create, and I fully support the above proposal. I'm sure we'll find a lot of people vested the current DNS oligarchy to critique, and possibly admire the work, but there's a very slim chance that we'll get them to allow us into their certificate chain, or even a user to control their own chain as authorative... <b>I do however, recommend that we aim to have each locally trusted Namecoin DNS server be considered 'the root authority' for their own users... And if the users don't like it, they can learn how to read. Then the local DNS server can 'sign' everything it finds in NMC space, and the browser can match the signatures.</b> -- during the transition to 'namecoin everywhere,' at least)</span>

--

<span style="font-size: x-small">Ugh, SSL, the kludgy hack that protects no-one.  This has been studied in-depth and it will need to be patched in the [http://www.thoughtcrime.org/blog/ssl-and-the-future-of-authenticity/ browser] ([http://www.youtube.com/watch?v=pDmj_xe7EIQ video overview]), probably using [http://convergence.io concensus from independent nortary systems] and heuristics to check the <b>quality of the security</b>, much like phishing and anti-virus protection.  Bottom line: this problem is much, much bigger than DNS and so will any solutions.  I don't see the harm in defining a mechanism for publishing public keys, but this should be a low priority issue for now.</span> --[[User:Indolering|Indolering]] 05:54, 18 August 2013 (UTC)

--

Using the cert fingerprint directly creates an unnecessary barrier to updates. Publishing an "authoritative" public key instead allows the domain owner to sign/update his own certs and revocations without having to update the blockchain.
This essentially makes it possible for each site owner to become his own CA, or if he doesn't want the hassle of maintaining his own secure infrastructure (e.g. air gapped signing computer that never sees the network), he can carefully choose a CA that he personally trusts, and let his visitors know about that choice.

All of the proposed content of these records could be replaced with a single [http://dnscurve.org/ DNSCurve] style NS record, which server could securely publish all of this info. (Using ECC instead of RSA is an important optimization: keys are smaller, encryption is faster and more secure.)

--

Could browsers be amended such that they check 3 places in order, first wins: 1) DNSSEC for an TXT (SSL) record,  2) namecoin record for the domain (obviously only .bit domains as of now), and failing that 3) check signature against CA certificates in the browse as normal. Those using the new systems do not need a root CA signed certificate, and legacy certificate will still work. 

In cases 1 and 2 the public half of the key used to self-sign the SSL certificate would be stored in the DNNSEC/namecoin record and can be trusted. With option 3, it's business as usual.

--[[User:Drak|Drak]] 14:49, 15 December 2013 (UTC)

--

== Discussion ==

=== 1023 character value limit ===

...

=== Whois ===

A public Namecoin WHOIS server is available:

$ whois -h whois.namecoin.us example.bit

TODO (Also, fix the <tt>info</tt> examples in the document accordingly.)

=== Legacy support ===

Below rules are supported, but will be obsoleted at one point:

* Sub-domains can be associated directly with String values, which will be interpreted as IPv4 addresses. E.g.: <tt>"map": { "www": "10.0.0.1" }</tt>
* <tt>ns</tt> is as an alias for <tt>dns</tt>.
* Empty sub-domain name (<tt>""</tt>) may be used to configure the domain. Entries specified here have lower precedence.


=== Implementation ===

TODO: Insert implementation notes here.

----

Some entries act like directives that change the way other entries are processed. In order of precedence, these are:

* <tt>delegate</tt> : Hands over control, so other entries can be ignored.
* <tt>import</tt> : Imports modify the currently processed object. Latest modifications have precedence.
* <tt>ns</tt> : DNS bridges hand over control, so other entries can be ignored. More sophisticated proxy resolvers that can serve non-IP services can resolve those addresses but need to ignore IP services that are hierarchically below this point. ''How this would actually work is not clear yet.''
* <tt>translate</tt> : Only affects sub-domain resolution. In effect, this causes the sub-domains in the <tt>map</tt> Object to be ignored. Addresses and other information in the domain context are still processed.
* <tt>alias</tt> : This works like the opposite of <tt>translate</tt>. It ignores the records in the current context and resolves to the referred address instead. Otherwise, sub-domains entries are still processed as usual. ''(Note: DNS bridge implementors must keep in mind that <tt>CNAME</tt> records cannot coincide with other records.)''

----

The topmost <tt>email</tt> record is to be used as the SOA e-mail address. <tt>email</tt> records of other entries can be converted to DNS <tt>RP</tt> records. This only involves DNS bridges. Whois servers could interpret this entry as the host master e-mail address.

----

Non-IP services, such as tor and freenet can be supported by a special resolver (e.g. in a proxy server). If a name resolves to both an IP and a hidden service, or even multiple different hidden services, the resolver needs to pick one.

----

=== EPP / OpenReg Prior Work ===
'''Definitely''' look at [[https://www.isc.org/software/openreg OpenReg]] from 2004 and the related EPP RFCs, 
[[http://www.faqs.org/rfcs/rfc3730.html  RFC 3730]] [[http://www.faqs.org/rfcs/rfc3731.html  RFC 3731]] [[http://www.faqs.org/rfcs/rfc3732.html  RFC 3732]] [[http://www.faqs.org/rfcs/rfc3733.html  RFC 3733]] [[http://www.faqs.org/rfcs/rfc3734.html RFC  3734]] [[http://www.faqs.org/rfcs/rfc3735.html  RFC 3735]].  Much of what's being written here has been mapped out already, and already has BIND integration. 

Similarities between OpenReg and this draft are quite apparent. If we can indeed take a large portion of this well thought out (but not marketed, obviously) system. Some changes would have to be done (replacing timestamps by block count, establishing multi-block translation and exchanges, and either removal of XML to something lighter or doing the enmc to point to EPP data)...

=== Security ===

The purpose of Namecoin is to provide a decentralized, yet authoritative database. It is certain that domains defined in the system are authentic and cannot be mangled by an unauthorized entity. (Although there may be caveats about this, they are outside the scope of this document.)

However, it's hard to be certain about the information we get from the external DNS servers supplied through <tt>dns</tt> entries. ''How to solve this properly is being discussed, please join in.''

One advantage of running on a locally authoritative system is that a certification authority for public keys isn't needed. ''(see #TODO)''


=== Mirror sites ===

A method to make mirroring of sites more manageable is proposed [http://dot-bit.org/forum/viewtopic.php?p=52&sid=9fd1c72effd2a46c2cc67cfc3474c91d#p52 on the forum].


=== Encoding ===

Current encoding for values is UTF&ndash;8 encoded JSON strings, which isn&rsquo;t very space efficient. 
A binary encoding like BSON wouldn't yield significant [http://bsonspec.org/#/faq space savings], but gzip compression is likely to give good results.

Gzip compression gives us variable savings, but we can expect to be able to fit between 1500 and 2000 characters in most cases.

In return, we lose the ability to strictly predict if a given record will fit in the blockchain, at least until the record is gzipped.

Implementing gzip compression in a backward-compatible fashion should be relatively transparent if done in namecoind itself. 
The magic number that starts a gzip stream cannot start a valid JSON payload, and can as such be reliably used to identify compressed streams.


ASN.1 would be a nice fit, and if we are dealing with X.509 certs, we already have a parser. Also permits compression. --LittleBrother

=== Future extensions ===

==== enmc ====

''Regarding [[#Encoding]]''

'''Proposed Question:''' How far do we really want to push the encoding? Being able to fit in like, I dunno, 3-4k would be nice, but there's a point at which the NMC system should just be handling the secondaries (+ a little extra) authority system that NS's almost everything secondary. 

'''Proposed Solution:''' Like EDNS, maybe we should allow 

<pre>
{
    "enmc" : {
            "map" : {"https://whatever/ns.txt", "http://blahblah.i2p/blah/ns.txt", "ftp://whatever.onion/ns.txt", "freenodeid","magnet://..." "finger://..."},
            "auth": { 
                     {hash: {"sha256": "c48c7a3388b98f8b999d9db4e8a5dc63fdc831183e0223b46a30886c514cb72d"}, "sig" : "yadayadya"},
                     {hash: {"sha256": "225e787582914bcbcf0eadb995bce7a555b99697b0d6a9babb3dcc443c371244"}, "sig" : "yadayadya"},
                     {hash: {"md5": "9f8d067fdb2373a64b4c3e420f31f4cc"}, "sig" : "yadayadya"}
            }
}
</pre> 

Where "sig" and "hash" will be a list of valid hashes and signatures OF THE FILE BEING DOWNLOADED (different protocols may result in different results)

The EDNS proposed above is quite fat, 569 bytes, but should still allow enough room in the other areas for a bootstrap of the domains used to get the files. 

''I don't think we need to waste more bits on saying which network is used to access the url, but hey, thats just me... They seem to all be quantum enough with what we have today'' - famous last words to be

*There's definite value in having self-contained records that don't triggered externally observable requests when resolved. Note that we have import and delegate mechanisms to spread the definition of a domain across several namecoin records.

* I'm in agreement. However these seem to mostly be NS record DNS based, and if we're branching away from DNS spec, then having alternative methods other then just plain NS->IP to access these records may be wise. I think it's ripe for abuse, and the "p/" trust would definitely have a part to play in if a resolver is interested in following those routes (either for trust or cost of accessing these networks). [[#EPP / OpenReg Prior Work]] may have some solutions, I haven't dug through everything over there yet. -ls

== TODO ==

=== Priority TODO ===

* Specifications of useful DNS records, such as <tt>spf</tt> and <tt>CERT</tt> are currently missing. (Note: <tt>spf</tt> record type will be converted to both <tt>SPF</tt> and <tt>TXT</tt> by DNS bridges.)
* Research into usefulness of technologies like <tt>NAPTR</tt> and <tt>HIP</tt> is needed before finalizing the specification.
* Ideas about the Namecoin PKI involves domains as well.
* TLS, how will will handle the time delay between revocation of a certificate and potential siezure of the private key. I mean, we could be talking about a good hour of signed traffic to the wrong party. Possibly an emergency alert broadcast system with abuse prevention (like alerts signed by your p/'s? and via web of trust?) ''(see my note above on using the public key instead of the fingerprint. Is your emergency broadcast idea going to be faster than the DNS TTL? If so, I predict that it will be ripe for abuse. --[[User:LittleBrother|LittleBrother]])''
* Development Versioning: I've dubbed the spec as was on 20110717 "DRFT20110717" or just "DRFT01" in my code internally. I got some decent work done parsing the current domains into a format that works with what's currently posted here, as well as what's acceptably used. While I did build in some flags to handle "sloppy A records" (SPEC01_TYPE_SLOPPY_ARECORDS).
* The 'ns' as an alias to 'dns' wasn't all that clear to me earlier, I think we make it more clear in the introductory text.
* Protocol/Software changes (within a single chain) should be done by a n% vote, where n > cost of starting an alternative chain (discussed below). It's wild west right now, but steady growth is going to require consensus. I don't care if we disagree on what's better, but I'd like to be able to log into your irc server at a named location to maintain diplomatic relations however corporate or dada they may be. Disconnection from diplomacy can only be assured by Layer 1 desperation (or in the event of war, a whole lot of Layer 0 NO CARRIERS)
* Make DNS Comic Part II (This should be high priority to garner support, I wish I were kidding) -- Looks like we [[http://i2psupport.org/ have a first drafter]], I like it! More would be great. I understand it completely; as will the I2P crowd, but people with limited literacy skills will not, nor will they take the initiative to understand it.

=== Thoughts on future TODOs ===
Like DNS, this is a 'long haul' project that could out live us if we're successful, after some reflection on what I worked with today, I do want to make sure this is a successful endevour. I would like to formalize the below with some market modeling, and eventually just remove this section, but for now, I mean, so few people read this part that it makes sense to multicast the thoughts here. I really need to learn the WikiMath symbols.  It's kinda hard to explain without the condensed symbols. '''Sorry for the tl;dr factor. These are thoughts that should be simulated before implementation.''' I'll try to throw together a TeX and <!-- What? -->, but first I want to finish the DRFT01->Zone first.

While DNS & BIND is a very 'hardened' beast (I read the BIND10 source this morning, after hating working on it's zone files for decades, now I hate this spec a whole lot more after gaining some respect for BIND while I try to write the ACTUAL transition :P).
*  I think we should '''equate a lot of the networks''', so that I can for instance, set an i2p signature hash on a names file as an NS record, likewise an .onion, a freenode file, I don't know the signature of a QRCode on an LCD tattoo on back of my neck.  I would also like to be able to delegate control of my names to alternative blockchains with alternative specifications of control linked via speed and hash power, so that we leave ourselves and our legacy an 'escape route' in much the same way as the original DNS protocol designers did.  Make it possible, don't make it too easy, to be assured that the follies of young markets are balanced with the wisdom of older ones, and cross that against the enthusiasm and energy of young markets balanced against the decadence and latency of the elderly ones (we all enjoy being young, we all never want to retire but eventually have to)... 

* '''Alternative Blockchains''' should have always have a symmetric FQDN via the path of delegation. Shortening the FQDN is accomplished by joining middle links at an exchange rate beneficial to the parties involved. A real FQDN should consist of a path made of some sort of (genesis block + key signing combo). The alternative blockchain initiator would be wise to save his first NMC for making quite a few 'NMC shorteners,' or a special alternative chain of NMC that is only for shorteners. This is going to be expensive like zone transfers, and ISPs should account for this in their budget.

* On the subject of '''linking alternative chains''', I think this has been addressed by the proposal for the BTC/NMC link, but we need to be sure that the .abc chain can grow to the size of .xyz chain, via the same path of growth as the original (but probably at a slower growth rate).  If I want to start a new name chain to delegate to, I should be able to split my computationally expensive power in half, and start growing a pool on my side. This also allows us to automatically increase the strength of the chosen block algorithm for my community to be exchanged with yours, in the event of ASIC hardware segregation. This should aim for a balance in always growing both trade and security at our block chain borders.

* After reviewing a lot of the BIND code, it makes a lot of sense at the core of how it was designed. They planned to be decentralized as well, and you can absolutely run a non-ICANN/ISC approved nameserver all you want, but... What they do have that we do not, is a lot of experience.  Verisign was in the right place at the right time to take control of the DNS Root Servers when all the .edu's ran out of grant money. Likewise, those of us who work on this in our free time, may find ourselves unable to keep pace and slowly relinquish control of our wallets (we all get old)... Our kids will take our blingy toy, abuse the hell out of it, then find themselves peeved when they find it in their adversaries/competitors hands. '''Learn from our forefathers mistakes and successes.'''

* '''We should absolutely take .bit and .nmc from the ROOT.''' I would suggest that Verisign offer it over kindly as a gesture of good faith so that we can profit all profit from strengthening the original intent of DNS and the potentially criminal monopoloy handed to them by the US Government (which is intended to increase and secure trade) and building a sane and safe plugin module for inclusion in the standard BIND distribution. I mean, I don't see any other solution to begin the fair transition into opening name services that keeps them in line with their stated goals. They may require a lot of paperwork, and a lot of annoyances, this might be not worth the hassle of someone just jacking the TLDs by putting better resources on the table, I would suggest that they look into a strategy promotes their own transparency. ''Those who make peaceful demonstration impossible, make violent revolution inevitable''

* Given the alternative-block-border thing above, and that it's in everyone's best interest to control as much of the media as possible, I would like to see the base NMC unit called the ''''SNash0'''' [sna sh oh]. S in tribute to the Satoshi, 'Nash' for [http://en.wikipedia.org/wiki/John_Forbes_Nash,_Jr. John Nash] (the good stuff is [http://www.math.princeton.edu/jfnj/texts_and_graphics/ over here]), and 0, for the equilibrium we hope this system will ride between the players. May wanna do .nash or .snash. I dunno, everyone seems to have a different idea for this. There will be a transition time while so that people with a stock setup can access NMC.
