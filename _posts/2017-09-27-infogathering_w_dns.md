---
layout: post
type: Project
category: Projects
tags: [lab, dns, reconnaissance]
title: Information Gathering with DNS
---

The information gathering phase of an attack is often overlooked when first learning about how to attack a device. It simply isn't as sexy as learning about all the fun exploits you can do once you've identified a vulnerable system. However, this phase is usually the most critical part of an adversary's strategy. If you don't understand your target, how do you know what to attack and how?

Of the types of information gathered during this phase, a target's IP address is one of the most critical. An IP address not only allows you to identify a target device and potentially map out the network but perform deeper scans to identify vulnerabilities you can then exploit. Each IP address identified is a potential entry point into the network, so the more IP addresses that can be found the better. DNS (Domain Name System) happens to be a service that is a gold mine of IP addresses.

<h2>About DNS</h2>
The primary purpose of DNS is domain name resolution: given a human readable name it provides the associated IP address. Thus DNS is an attractive target for attackers as it is not only full of IP addresses, but also maps those addresses to domain names on the network. 

<span class="notation"><strong>Fun Fact:</strong> Today, DNS is the backbone of the Internet. Back in the days of ARPANET though, computers only had a hosts file to map IP addresses to hostnames (Linux: /etc/hosts, Windows: C:\Windows\System32\drivers\etc\hosts). The hosts file worked well for small networks, but proved difficult to scale as networks grew larger and more complex. Thus DNS was developed to replace the hosts file. The hosts file is still used on pretty much every OS you run into. If you want to do some quick and easy DNS poisoning this is the file to update.</span>

<h2>DNS Lookup</h2>
A DNS lookup queries the DNS server for the IP address associated with a specific domain name. If the DNS server doesn't have the DNS record in question, it passes the request on to a parent DNS server. The root servers at the top of the DNS heirarchy serve as the last resort for finding a DNS name. The root servers host top level domains such as ".com", ".org", ".edu" and the like. The lookup process at a very high level can be broken down to the following steps:

1. Check local hosts file and local cache
2. If not found, send a request to the local DNS server
3. If not found, the local DNS server does a recursive search through the DNS heirarchy
4. The return value from the local DNS server is then locally cached

For a more detailed look at these steps, checkout <a href="//blog.catchpoint.com/2014/07/01/dns-lookup-domain-name-ip-address/">this link</a>.

For visual effect, I captured the wireshark output of a dig command I ran on this site. You can see that command queried all three of the DNS servers my ISP has me using by default and all three of them responded with the correct information. All DNS lookups are done with UDP on port 53 of the DNS server.

<img src="/images/posts/2017-09-27/dns_lookup_pcap.png" alt="dns lookup pcap"/>

<h2>Commands</h2>

<h3 style="font-size: 120%">whois</h3>
Domain registrars run a whois service which provide information about the owner of a domain. You can run the whois command on a domain and it provides at the very least details on what registrar the domain is registered with. It can also provide contact information as well as DNS and mail servers for the domain in question. One issue you may run into with whois is that the content it returns really depends on the registrar running the whois service. Some registrars are also scaling back on what data is made publicly available due to (legitimate) privacy concerns.

Here is what I get if I run whois on my own website:

<img src="/images/posts/2017-09-27/whois_lp_net.png" alt="whois command"/>

You can see that with this command, I can confirm that my domain is registered with Google as well as when my domain will expire. You can also see that I use Cloudflare as a DNS server (it provides HTTPS for my site). If I go to domains.google.com (the registrar URL), I can then lookup my domain on their site and find even more information such as the below admin and tech contact information:

<img src="/images/posts/2017-09-27/registrar_whois.png" alt="registrar whois lookup"/>

Of course, I set all this information to private when I setup my domain. So default information is being provided in these fields rather than anything legit. However, not everyone is as cautious with their data. The whois command is really just the first step to learning more about a domain and getting to the treasure trove of data in the DNS server. 

Another source of IP information available to us is <a href="//www.arin.net">ARIN (American Registry for Internet Numbers)</a>. ARIN is the regional internet registry for the U.S. and Canada. You can find information about blocks of IP addresses on the database hosted on their website. This will give you an idea of what IP range is assigned a specific domain. 

<h3 style="font-size: 120%">nslookup</h3>
The nslookup command is used to query the DNS to lookup DNS records. Here are some of the record types which can be queried:

<table>
	<tr style="font-weight: bold;"><td>Record Type</td><td>Description</td></tr>
	<tr><td>A</td><td>IPv4 address</td></tr>
	<tr><td>AAAA</td><td>IPv6 address</td></tr>
	<tr><td>CNAME</td><td>Canonical name for alias</td></tr>
	<tr><td>HINFO</td><td>Host CPU and OS Type</td></tr>
	<tr><td>MX</td><td>Email Server(s) in the domain and preference rating for servers</td></tr>
	<tr><td>NS</td><td>DNS Servers and IP addresses</td></tr>
	<tr><td>SOA</td><td>Start of authority - primary name server in domain</td></tr>
	<tr><td>TEXT</td><td>Text messages, SPF (sender policy framework) information designed to detect email spoofing</td></tr>
	<tr><td>ANY</td><td>union of above</td></tr>
</table>

I can run nslookup and get an interactive shell. I can then set the type to any of the ones listed above to filter my query on. Below shows me setting the type to "any" and then querying google.com:

<img src="/images/posts/2017-09-27/nslookup_google.png" alt="nslookup google"/>

Additionally, you can lookup specific record types as shown below with my own website:

<img src="/images/posts/2017-09-27/nslookup_lp_net.png" alt="nslookup lp.net"/>

<h3 style="font-size: 120%">dig</h3>
Dig is pretty similar to nslookup and can do pretty much all the fun things nslookup can do. You can see below that dig can take the same type parameter (-t) as nslookup and shows the same results for google.com:

<img src="/images/posts/2017-09-27/dig_google.png" alt="dig google"/>

Many people prefer dig to nslookup (easier CLI), but they both offer similar results.

<h3 style="font-size: 120%">host</h3>
Another simple lookup tool is host. Host acts as a basic hostname/IP address resolver. Below shows the equivalent type=any command for host on google.com:

<img src="/images/posts/2017-09-27/host_google.png" alt="host google"/>

<h2>Zone Transfers</h2>
Zone transfers are used to replicate the DNS database across multiple DNS servers. A secondary DNS server will request a zone transfer from the primary DNS server in order to keep its database in sync with the primary. This is done using a the special AXFR query type over TCP (still port 53). Zone transfers are by default permitted by any host on the network, but most DNS servers can be configured to block transfers or require authentication when a transfer is requested. If this is not setup, however, zone transfers can be used by an attacker to map the entire topology of the network.

Below shows me successfully completing a zone transfer request with dig in a lab environment. The bind server happily shares the entire network topology with my Kali box: 

<img src="/images/posts/2017-09-27/zone_transfer.png" alt="dig zone transfer"/>

When a zone transfer fails you, there are other tools out there you can turn to get information out of DNS. The fierce command is one of them and comes pre-installed on most Kali installations. Fierce is a Perl script that first tries a zone transfer on the targeted domain. If that fails it then attempts to brute force the host names by sending a series of DNS queries to the DNS Server. This assumes you use a typical naming convention for hosts on your network.

So if I update my bind configuration on my DNS server to block zone transfers from untrusted devices, I get the following error when I attempt the same zone transfer command with dig:

<img src="/images/posts/2017-09-27/zone_transfer_fail.png" alt="dig zone transfer fail"/>

However, if I try again with fierce:

<img src="/images/posts/2017-09-27/fierce.png" alt="fierce"/>

Fierce failed too. This is because I used abnormal naming conventions in my lab's DNS. If I had used more typical ones like "support" or "mail", fierce would certainly have been more successful.

<h2>References</h2>
<i>The Basics of Hacking and Penetration Testing</i>, 2nd Edition, Patrick Engebretson, 2013
