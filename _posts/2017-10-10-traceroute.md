---
layout: post
type: Project
category: Projects
tags: [lab, traceroute, tcp/ip, scanning]
title: Scanning with Traceroute
---

Traceroute is a tool most network and system administrators are familiar with and comes installed on most operating systems by default. Traceroute allows you to follow the path a packet takes through a network, identify any routers or other devices along that packet's path, and determine the network latency between devices on that path. These capabilities are obviously pretty handy for debugging a network. However, from an attacker's perspective this tool is also useful for mapping a private network in order to identify potential targets for exploitation.

<h2>How Traceroute Works</h2>
The network layer (layer 3 in the OSI model) is responsible for routing data between physical networks. There are a couple common protocols you see at this layer including ARP, IPv4, IPv6, ICMP, and ICMPv6. Traceroute takes advantage of the nature of IP (Internet Protocol) and ICMP packets to gather data on the network. IP packets all have an IP header. In this header, there are a variety of fields used to help a packet reach the correct destination. The IP header is shown below:

	 0                   1                   2                   3   
	 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |Version|  IHL  |Type of Service|          Total Length         |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |         Identification        |Flags|      Fragment Offset    |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |  Time to Live |    Protocol   |         Header Checksum       |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |                       Source Address                          |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |                    Destination Address                        |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |                    Options                    |    Padding    |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

Traceroute primarily utilizes the Time to Live (TTL) field in this header. The TTL field defines the number of routers the packet can traverse (hops) before it is discarded. TTL is defined when a packet is created and decremented by 1 each time a packet is forwarded by a router. If TTL hits 0, the router drops the packet and uses ICMP to inform the packet's sender that the packet's TTL has been exceeded. Without TTL, a packet would be forwarded around the network in a never ending loop if its destination is unavailable or the path to the destination is misconfigured. A network without TTL would slowly accrue these rogue packets until eventually the network runs out of bandwidth, triggering a denial of service condition. Shown below is a snapshot of an IP header in Wireshark that shows the TTL field:

<img src="/images/posts/2017-10-10/wireshark_ip_ttl.png" alt="Wireshark IP Header"/>

Traceroute takes advantage of the fact that when TTL expires the router will always inform the original sender. This response will include a source address corresponding the address of the router where the packet died. So in order to find all the hops between the sender and destination, traceroute need only send packets to the destination with a range of TTL sizes between 1 and a large enough number n until the final destination is reached and a TTL exceeded response is not returned.

<h2>Traceroute in Action</h2>
Traceroute takes a source address (the host its running on), a destination address, and an invalid destination UDP port (between 33,434 and 33,534) and crafts UDP echo request packets with a gradually increasing TTL (starting at 1 and increasing by 1). As each UDP packet's TTL is exceeded, traceroute records the source address of the response as well as the time it took for that router to respond. Below shows you traceroute run on Google's IP address, you can see that there appear to be 5 routers (or similar device) between my Kali VM and Google's server.

<img src="/images/posts/2017-10-10/trcrt_google_ip.png" alt="Traceroute to Google"/>

You can watch traceroute in action with Wireshark to see what's happening behing the scenes. Below you can see that after traceroute is run on the 75.114.81.1 destination, it begins to send sets of packets with incrementing TTLs and invalid UDP ports:

<img src="/images/posts/2017-10-10/wireshark_start_trcrt.png" alt="Wireshark Traceroute UDP Packets"/>

Eventually, it begins to receive "time-to-live exceeded" ICMP responses complete with the source address of interest from the responding routers:

<img src="/images/posts/2017-10-10/wireshark_ttl_trcrt.png" alt="Wireshark Traceroute TTL Exceeded"/>

Finally, the traceroute command completes when the UDP packets with TTL set high enough reach the destination which then responds with destination unreachable (port unreachable) ICMP packets since the IP address is reachable but not the invalid UDP port:

<img src="/images/posts/2017-10-10/wireshark_icmp_trcrt.png" alt="Wireshark Traceroute ICMP Invalid Port"/>

<h2>Traceroute through a Firewall</h2>
Since traceroute by default relies pretty heavily on a set of UDP ports and ICMP, it is pretty straightforward to block its scan from getting into or out of the network. Indeed most firewalls with any type of semi-decent configuration should block these ports by default. Below you can see how a firewall configured to block some ICMP ports (pings) and UDP ports (everything except DNS) can trip up traceroute:

<img src="/images/posts/2017-10-10/trcrt_fail.png" alt="Traceroute Blocked By Firewall"/>

Above you can see that my pseudo firewall (it's really a CentOS box with iptables setup) responds to TTL 1 without a problem but blocks any packets with a higher life expectancy from getting back to traceroute. Traceroute then is stuck waiting forever for a response (trying to account for network latency).

Is that it for traceroute? Of course not. Firewalls have to let <i>something</i> through otherwise the network is pretty useless. Instead of relying on invalid UDP ports, you can use a port you know that the firewall lets through. In my lab's case, a good option is TCP 80 since I have a web server set up on the other side of the firewall which I want external users to be able to access. All I need to do is add the tcp flag to traceroute. It selects port 80 by default, but I could set it to any TCP port. Traceroute then does the same old trick with TTL, but with a TCP "SYN" packet instead which gets through the firewall without issue:

<img src="/images/posts/2017-10-10/trcrt_tcp.png" alt="Traceroute on TCP"/>

You can see that traceroute was able to complete the path. My lab's network isn't that exciting as it only has one hop over the external router to the web server, but it demonstrates the point. This scan still relies on that "time-to-live exceeded" ICMP response, it just uses a different flavor of packet to poke the network. In general, it is not considered wise to completely block these ICMP responses as TTL is pretty essential and without it things tend to break. 

From a defensive perspective, I wouldn't bend over backwards trying to block these kinds of scans from getting into your network. It's important to remember that traceroute is a tool your own team uses to debug network issues. Honestly, if your network's primary defenses rely on the fact that the adversary doesn't have a complete list of the IP addresses in your network, you are doing security wrong.

<h2>References</h2>
<i>Practical Packet Analysis</i>, 3rd Edition, Chris Sanders, 2017


