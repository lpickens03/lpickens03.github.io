---
layout: post
type: Project
category: Projects
tags: [lab, scanning, ports, nmap, netcat, netstat]
title: Port Scanning
---

When either defending or attacking a system, it is important to learn as much as you can about it. One way to do this is with port scanning. There are up to 65,535 ports which could be in use on a system. Understanding what these ports are and how they are used is critical from both a defensive and offensive perspective.

This post covers some common/useful commands for port scanning.

<h2>LSOF</h2>
lsof provides a list of all open files belonging to all active processes.

<code>lsof -i :&lt;port&gt;</code>
<br/>Lists all processes listening on a specific port. Includes process id, making it pretty easy to kill if you need to.

<img src="/images/posts/2017-09-16/lsof_i.png" alt="lsof command"/>

<h2>NETSTAT</h2>
From a host, you can list out all network connections on a system. 

<h3 style="font-size: 120%">Commands</h3>

<code>netstat -a</code>
<br/>Lists all connections

<code>netstat -at</code>
<br/>Lists all TCP connections

<code>netstat -au</code>
<br/>Lists all UDP connections

<code>netstat -tl</code>
<br/>Lists all listening TCP connections

<code>netstat -ant</code>
<br/>Disables DNS reverse lookup for faster output

<code>netstat -antp</code>
<br/>Includes process/PID in output

<h2>NETCAT</h2>
Netcat is the self professed "swiss army knife" of TCP/IP (see it's man page). It can create almost any kind of TCP or UDP connection you could need, making it a fantastic debugging/exploration tool.

<h3 style="font-size: 120%">Commands</h3>

<code>nc &lt;host&gt; &lt;port&gt;</code>
<br/>The simplest usage creates a TCP connection to a given host and target port. Any standard input you then type will be sent to the host. Below you can see me establishing a TCP connection on port 80 of a Metasploitable VM and then sending a GET request to that port and receiving the apache server's response.

<img src="/images/posts/2017-09-16/nc_80.png" alt="netcat command"/>

Similarly, you can run this command with the verbose flag (-v) to get more information on the port.

<img src="/images/posts/2017-09-16/nc_v_80.png" alt="netcat verbose command"/>

<code>nc -lvp &lt;port&gt;</code>
<br/>From a host you can also listen (verbosely) on a port. You will then be able listen to and send traffic on that port (as shown below, I sent myself a message with netcat on the port I was listening on with netcat).

<img src="/images/posts/2017-09-16/nc_lvp.png" width="600" alt="netcat listen on port"/>

<img src="/images/posts/2017-09-16/nc_send_msg_on_port.png" width="300" alt="netcat send message on port"/>

<h2>NMAP</h2>
Probably the best known and most powerful port scanner. For a great reference guide best to go to the <a href="//nmap.org/book/man.html">source</a>.

<h3 style="font-size: 120%">Commands</h3>

<code>nmap -sS &lt;host(s)&gt; -oA &lt;filename&gt;</code>
<br/>TCP SYN Scan.

<img src="/images/posts/2017-09-16/nmap_ss.png" alt="nmap TCP SYN scan"/>

Additonally, you can scan for a specific port.
<img src="/images/posts/2017-09-16/nmap_ss_p.png" alt="nmap TCP SYN scan"/>

<code>nmap -sU &lt;host(s)&gt; -oA &lt;filename&gt;</code>
<br/>UDP SYN Scan. Note that this scan takes longer than TCP. You can hit the up arrow while it's running to check on it's status. Just take time remaining at face value.

<img src="/images/posts/2017-09-16/nmap_su.png" alt="nmap UDP SYN scan"/>

<code>nmap -sV &lt;host(s)&gt; -oA &lt;filename&gt;</code>
<br/>Version Scan. Rather than just scanning to see if ports are open, version scan pulls banners and other information after establishing a TCP session to make a best guess on the version of the service running on the open port.

<img src="/images/posts/2017-09-16/nmap_sv.png" alt="nmap Version scan"/>



