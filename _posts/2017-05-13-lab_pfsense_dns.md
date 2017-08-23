---
layout: post
type: Project
category: Projects
tags: [lab, firewall, dns, dhcp]
title: Setting Up DHCP and DNS with pfSense
---

My ISP wireless router is set up in the main living area. My lab, on the other hand, is on the second floor. I'd like to be able to access the internet from my lab network without having to open up the walls to string Cat5s or lay an extra long Cat5 up the stairs, thru the hallway and under the door to the lab switch. Additionally, with the loss of my "air gap", I want to put in place a security solution (or at least phase 1 of a solution) to mitigate the risk of exposing my lab directly to the internet. 

Thus I arrived at my solution: use a wireless bridge to connect my ISP wireless router to a firewall which will then be connected to my lab switch to provide internet to all. So my updated lab configuration will look something like this:

<img src="/images/posts/2017-05-14/lab_phase_1.png" alt="Lab Phase 1 Network Diagram"/> 

The benefits of a firewall are three-fold: the ability to filter incoming and outgoing traffic, to hide my private lab network from the internet with NAT, and provide me a proper firewall to play with rather than just the VyOS VMs I've had to play with so far. I chose pfSense for the firewall simply because it's an open source solution that comes highly recommended and it's something I haven't played with yet. I am trying to stick to open source solutions as much as possible with my lab to save some money (another reason I'm using VirtualBox as a hypervisor for my VMs rather than VMWare). 

Setting up the wireless bridge and pfSense was actually pretty straight forward. In the end what actually cost me the most time and headaches was getting my DNS configuration right. Since I couldn't find a guide online that fit my particular needs, I've documented my setup here.

<h2>Setting Up DHCP</h2>
I was going back and forth for a while on whether I should use pfSense's built in tools to set up DHCP and DNS on my network. In the end, I decided to give it a shot rather than set up a separate VM running a DHCP and DNS server. First, I had to make sure DHCP was disabled on both my wireless router and the new wireless bridge. Running two DHCP servers on the same network can and will cause IP conflicts and break the network unless they are properly configured to split the scope of IP addresses on the same subnet. For my small lab net, this is unnecessary. 

pfSense provides a pretty easy way to set this up. First, you go to Services -> DHCP Server. The page for configuring DHCP for your LAN connection will be displayed. Here you will enable DHCP for the LAN interface and it will use the current IP settings for that LAN to decide what IP range it will offer hosts requesting IP addresses. If you scroll all the way to the bottom of this page, you can add static IP mappings so that hosts on the network will always receive the same IP address each time they boot up. All you need to do is hit "Add", fill in the host's MAC address and the desired IP address as well as it's host name and you are good to go. 

<h2>Setting Up DNS</h2>
This is where I ran into problems. DHCP conventiently instructs clients on where to look for DNS and by default points them all to the pfSense as the primary DNS server. pfSense has two DNS options: the DNS Forwarder and the DNS Resolver. DNS Forwarder just forwards all DNS requests to another DNS server with recursive caching capabilities using bind. DNS Resolver uses Unbound as a recursive caching DNS server with DNSSEC capabilities. By default, pfSense has the DNS Resolver configured on the LAN to handle DNS requests to the DNS servers configured by your ISP provider on the WAN connection. This works fine as is until you want to add internal DNS mappings to pfSense. Really what it came down to is that I needed an external DNS server to handle internet DNS requests and an internal DNS server to handle local DNS requests. I found a few guides that claimed pfSense could act as the internal DNS Server and forward internet requests to the external DNS Servers assigned by the ISP with DNS Resolver. However, the performance hit when loading web pages as well as the reduced reliability of DNS in general made this solution intolerable. 

In the end, I disabled both pfSense's DNS services and checked the "Do not use the DNS Forwarder/DNS Resolver as a DNS server for the firewall" on pfSense's general setup page. I then set up my own internal DNS server using bind9 on an Ubuntu 16.04 box on my network. This server I set up to forward requests to a set of external DNS servers. Since your ISP's DNS servers aren't always the fastest ones available I followed <a href="//www.ceos3c.com/2016/11/29/improve-surfing-speed-with-namebench/" >this guide</a> to use the namebench tool to find my top three DNS servers for internet DNS requests. Google's Public DNS, Norton's DNS, and RoadRunner's DNS offered me the best performance.

Following <a href="//www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-16-04" >this tutorial</a>, I then set up bind9 on my Ubuntu box. The only addition I made to the steps outlined in the link (aside from changing the IP range and zone names to match what I had set up on the pfSense DHCP server) is updating the forwarder section under the options section in the named.conf.options file with the list of three external DNS servers found with namebench. I then updated the DHCP server on pfSense to set this Ubuntu box's IP address as the DNS Server for all client's requesting IP addresses. 

After renewing my DHCP leases on each of my DHCP clients, things were looking good except for the Ubuntu box running the DNS server. When I tried to ping anything from there I got the special IP address "127.0.53.53". This indicates that I am getting DNS name collisions on my internal network (the 53 in the IP address implies port 53, the DNS server port). I had a fun time figuring out that Ubuntu's Network Manager's default settings automatically configures the dnsmasq program as a local forwarding nameserver listening on 127.0.1.1. Meaning I was actually running two DNS services on Ubuntu causing these collisions. I had to edit /etc/NetworkManager/NetworkManager.conf to comment out "dns=dnsmasq" and then restart the network-manager service to clean up the list of DNS servers it was generating in /etc/resolv.conf.

With these last few changes made, I had a happy network with internet access and host names and IP addresses resolving correctly. I could now focus on actually configuring pfSense to be a firewall (it's default rule is your typical "allow all" making it completely useless as a security measure without some time spent configuring it). That, of course, will be another post.





