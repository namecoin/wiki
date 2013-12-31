JavaScript DNS is a client-side/browser DNS resolver which complies with the Namecoin [[domain name specification]].  This is the specification outlining the expected behavior of a jsDNS and any related APIs.

Note that this poorly written specification is written according to [Joel Spolsky's excellent specification specification](http://www.joelonsoftware.com/articles/fog0000000035.html).  Bad jokes, terrible puns, poor grammar, and revisable facts (i.e. revisions to the spec) lay ahead.

# Preamble
You should totally skip this section!
## Why Bother
Let's say you want to bridge two DNS roots, like, I don't know ... ICANN and Namecoin.  Previously, you had two basic options:

1. Mirror the content (we are going to lump generic URL forwarding into this category, as it's basically the same thing legally speaking).
2. Proxy the content.

There are many, many problems with these choices.  Here are three of them:
1. They either link to or host content that will piss off [idiotic judges](http://en.wikipedia.org/wiki/Bank_Julius_Baer_vs._WikiLeaks), equally idiotic [politicians](http://wikileaks.org/tpp/#QQC12), and [evil lawyers](http://www.chillingeffects.org/domain/faq.cgi#QID226).
2. Web-proxies are crazy insecure, both the client _and_ the origin server have trust the proxy to decrypt and re-encrypt everything.
3. Both options scale linearly: for every request on the .bit namespace a single charity-driven website has to respond.  With a jsDNS, you can have a **single static web-page** which scales at log(n) (aka [webscale](http://www.mongodb-is-web-scale.com/)!!!!!).

## So install Namecoin!
Right, because telling people to install/change a system setting has worked so well for [AlterNic](http://en.wikipedia.org/wiki/AlterNIC), [OpenNIC](http://www.opennicproject.org/), [and every other Alternative DNS system](http://en.wikipedia.org/wiki/Alternative_DNS_root) to have pooped up over the past ~20 years.