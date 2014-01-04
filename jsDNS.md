# Javascript DNS
JavaScript DNS is a client-side/browser DNS resolver which complies with the Namecoin [[domain name specification]].  This is the specification outlining the expected behavior of a jsDNS and any related APIs.

Note that this poorly written specification is written according to [Joel Spolsky's excellent specification specification](http://www.joelonsoftware.com/articles/fog0000000035.html).  Painful puns, bad jokes, sloppy grammar, and revisable facts (i.e. revisions to the spec) lay ahead.

# Why Bother
Let's say you want to bridge the the ICANN and Namecoin DNS'.  The basic approach is to replace a Namecoin [Top Level Domain](http://en.wikipedia.org/wiki/Top-level_domain) (i.e. `.bit`) with an ICANN Second Level Domain (i.e. `.bit.pe`).  Previously, you three ways to accomplish this:

1. Proxy the content.
2. Mirror the content.
3. URL forwarding.

There are many, many problems with these choices.  Here are some of them:

* They either link to or host content that will piss off [idiotic judges](http://en.wikipedia.org/wiki/Bank_Julius_Baer_vs._WikiLeaks), equally idiotic [politicians](http://wikileaks.org/tpp/#QQC12), and [evil lawyers](http://www.chillingeffects.org/domain/faq.cgi#QID226).
* Web-proxies are crazy insecure: both the client _and_ the origin server have trust the proxy to decrypt and re-encrypt everything.  It's not _like_ a man-in-the-middle-attack, it _is_ a man-in-the-middle attack.
* They all scale poorly: for every request on the .bit namespace a single charity-driven website has to respond.  With a jsDNS, you can have a **single static web-page** which [scales at log(n)](https://docs.google.com/spreadsheet/ccc?key=0Am9LbxJR3-Q-dFQ3c2lJa1BVejdrT2JnZjBRWm83YlE&usp=sharing) (aka [webscale](http://www.mongodb-is-web-scale.com/)!!!!!).

So why not just install Namecoin?  Well, telling people to install software/change a system setting hasn't worked very well for [AlterNic](http://en.wikipedia.org/wiki/AlterNIC), [OpenNIC](http://www.opennicproject.org/), or any [other alternative DNS system](http://en.wikipedia.org/wiki/Alternative_DNS_root) to have popped in the past ~20 years.

Not impressed with my bullet points?  Check out this biased, out-of-context feature matrix!

| Backend                | Domain Name System Resolver                                   | End-user requirements                 | Linkable by ICANN sites | Absolute .alt URL   | Direct Https | Verifiable Privacy  | Scaling   | Additional .alt website config         | DMCA Liability   | Domain Seizure Reistantance | SOPA Passive Censorship Resistance | Chinese National Firewall Censorship Resistance |
| :--------------------  | :-----------------------------------------------------------: | :-----------------------------------: | :---------------------: | :-----------------: | :----------: | :-----------------: | :-------: | :------------------------------------: | :--------------: | :-------------------------: | :--------------------------------: | :---------------------------------------------: |
| Custom DNS/User Config | DNS Servers                                                   | Sys Config                            | No                      | Full                | Full         | No                  | Log       | None                                   | Linking          | Full                        | Partial                            | Minimal
| Proxy                  | Webhost                                                       | None                                  | Yes                     | Partial-Full        | None         | No                  | Linear    | None                                   | Linking          | Partial                     | Minimal                            | Minimal
| Mirror/NS              | Server                                                        | None                                  | Yes                     | None                | Full         | Yes                 | Sub-Linear| NS change, CORS                        | Linking, hosting | Partial                     | Minimal                            | Minimal
| jsDNS Best-Effort      | Client storage, WebHook sourced from social network(s) or DHT | Onetime Social Network Log-In or none | Yes                     | None                | Full         | Yes                 | Constant  | Dedicated IP or ICANN URL              | None             | Full                        | Minimal-Full                       | Minimal
| Cooperative jsDNS      | Client storage, WebHook sourced from social network(s) or DHT | Onetime Social Network Log-In or none | Yes                     | Full                | Full         | No (maybe)          | Constant  | Dedicated IP or ICANN URL + JS include | None             | Full                        | Minimal-Full                       | Minimal


#Use-cases
Giving context and explaining the shortcomings of the above feature matrix requires understanding the user experience, so let's create some users!  Note that the following scenarios are written in an absurdist manner because the examples they are based on are depressingly real.

##Scenario 1: Justin Bieber
Justin is a hard working musician who likes to read about himself on music blogs.  To that end, he "leaks" songs to the blogs which write about him the most.  Unfortunately, his favorite blog, _BieberBabble.com_, had it's domain name seized in a [recent ICE "raid"](https://www.aclu.org/blog/free-speech-national-security-technology-and-liberty/ice-domain-name-seizures-threaten-due) of websites infringing on Justin's songs.

Thankfully, the Bieber Babble admins got themselves a shiny new .bit domain and told Justin to check them out at BieberBabble.bit.pe.  Justin types in BieberBabble.bit.pe and finds not his favorite music blog but a webpage asking him to login to Facebook, Twitter, or another social network.  Justin LOVES social networking and he quickly logs in.  Among his thousands of fans on Facebook, the Speech.js script finds a few friends that like a page which publishes links to Namecoin nodes.  Given the high consensus among his fanbase, Speech.js checks the link automatically, the URL it returns a valid record (UXTO/SCP) record, and Speech.js loads the URL in a child iFrame.

###Technical Note
The social networking backend is primarily to avoid directly linking or hosting any content that could trigger the legal headaches of DMCA-like take-down requests.  The free publicity of online slackervism is very attractive as well.  Even so, using a DHT to bootstrap the client may be preferable.  The legality of the various backends is awaiting clarification.

Ratery Wandhouse
## Scenario 2: Wandy Raterhouse
Wandy is a HUGE fan of Justin Bieber and was reading about Bieber's pet monkey on BieberBabble.bit.  At the end of the article was a link to the blog's pet monkey store and (of course) Wandy _needed_ to buy one for himself.  Given the expense of such pets, the admins at BieberBabble.bit decided they needed a more secure setup  So when Wandy clicked on the link pointing to `store.BieberBabble.bit.pe` Speech.js read the record and instead forwarded the **parent** frame the link to `store.jeiberjabbers.net`, reasoning that they can always change the links to the store if jeiberjabbers.net is seized.

## Scenario 3: Jean Merryd
For the good of their subjects, the Monarchy of Great Britain has decided to blacklist all [Bieber related material](link).  This is a depressing reality for Jean, a London resident with a major case of Bieber Fever.  When attempting to visit BieberBabble.bit.pe Jean is confonted with an error page stating that the Crown has decided to censor the website.  Initially depressed, the website gave Jean some hope: if she connects the jsDNS with her email or social networking account Jean can visit BieberBabble.bit by visiting `bit.pe#BieberBabble`.  Jean logs into Gmail and the jsDNS sends a PGP encrypted email to BieberBabble admins.  The reply email includes a URL with basic HTTP authentication which the jsDNS stores as the primary source for BieberBabble.bit DNS information and a secondary source for all other domains.  The jsDNS checks this address and, like magic, receives a signed DNS record with a personalized URL to the site.  This all happens invisibly to Jean whom is reading the latest scoop of BieberBabble related material within minutes of connecting to her email account.

## Scenario 4: Hanah Kal
Hanah's parents have taste and do not wish their daughter to catch Bieber Fever, or at least exhibit signs of it when they are around.  So they install a security program on the family computer that prevents her from opening file sharing programs as well as blocking Bieber related websites and work-arounds like a jsDNS.  Hanah is screwed: she will have to feed her Bieber fever via a sneakernet with her friends at school.  That's okay, however, because those are her parents making rules about use of their computer not blanket censorship of an entire nation.

# Goal Setting
The following goals are not likely to be supported in the immediate future:

## Non-Goals
* Bypassing "active" corporate or national firewalls such as that of China.  
* Bypassing of DNS-level blocking of the resolver site (such as `.bit.pe`, `.spx.is`, or `speech.is`).

## Future Goals
Future development may enable the following features:

###Verification by Child iFrame
Verification of the parent page's content by the child iframe is not possible in current browsers. This would, however only require a minor extension to the CORS or Frame SECURITY OPTION to enable either read-only access to the parent window's document or a special extension to retrieve hash signatures of the parent page.

### Preventing Server-Side MITM Attack
Using a jsDNS requires trusting the host which delivers the jsDNS script.  There is no way to require the browser to check multiple independent servers for a single domain, a herd-style consensus mechanism, nor signed "binaries" (although TLS provides a similar kind of encryption).  However, all three of these could be enabled by the server and the infrastructure for them (multiple, independent content hosts, publication of hash signatures, etc) should be defined and supported.

### P2P Content Delivery
Bypassing of DNS-level blocking of known IP addresses or ICANN sites listed in the `translate` field (which is what jsDNS reports to the browser to source the content) using JavaScript P2P communication.  **This would likely be a centrally managed swarm managed by the domain owner from a known IP addresses listed in the blockchain**.  However, since the management servers themselves are blacklisted by a central authority, a DHT (either piggy-backing off of existing DHT's or a custom network) would connect users suffering in countries with internet censorship to visitors located in countries without filters.  All content would be signed with a key listed in the blockchain or encrypted via TLS, the prior enables simple caching of static content.

The above suggestion for routing around IP-level censoring does not anonymize the activities of the end-user.  As the blockchain provides a trusted computing base and clients are likely to be rather ephemeral (reducing the time available for connection setup and tear-down) multi-layer onion encryption is rather wasteful.  An overlay network based on [Random Linear Network Coding]() would provide a highly decentralized method of anonymous communication which is resistant to timing analysis while providing throughput near the theoretical maximum.  It would then be easy to mix background traffic (DNS updates, DHT information, and caching common resources such as JavaScript libraries) with the specific content desired by the client .

# Flowchart
Conceptual overview of each step of the routing process goes here.

# Wireframes
This is a screen-by-screen mockup of visiting a website using a jsDNS.  Most of the interface is invisible and the exact details of the interface will depend on the backend being used as a DHT, Twitter, Github, and Email all have very different requirements.  This is mostly to elucidate the technical aspects of what happens at each step of the lookup process.

# Technical Note: HTTPS -> HTTP Downgrade

# Technical Note: Scaling

