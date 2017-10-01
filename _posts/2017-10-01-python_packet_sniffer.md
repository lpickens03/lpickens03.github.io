---
layout: post
type: Project
category: Projects
tags: [lab, python, socket, tcp/ip]
title: Packet Sniffing with Python
---

As part of my efforts to write my own packet sniffer, I wanted to take a look at handling raw packet data. I am hoping to eventually evolve this script into a tool to actually modify packet data before it is sent out. Fortunately, the Internet is vast and I found some great tutorials and example scripts to help me on my way. This post documents the steps I used to write my own basic packet sniffer in Python 2.7. 

<h2>Socket Basics</h2>
In it's simplest form, socket programming is connecting two nodes (aka sockets) on a network so that they can communicate with each other. These sockets can be categorized into two groups: the client and server. The server listens on one port and IP address. The client sends requests to the server, prompting the server to respond.

Sockets can be implemented on any of a number of different channels. Of primary interest to me are UDP and TCP so my focus will be on these protocols.

To create a socket you first import the socket module and then define your socket:

	import socket
	s = socket.socket(<socket_family>, <socket_type>)

You can follow a tutorial found <a href="//www.tutorialspoint.com/python/python_networking.htm">here</a> to set up a basic server/client model with socket. When you run your server it sets up a listener on your localhost on whatever port you set it to (I set mine to 1337). I then ran lsof to show that my server socket is listening on TCP port 1337:

<img src="/images/posts/2017-10-01/socket_server_output.png" alt="server is running"/>

When I run my client socket, it connects to the server. Once connected, the server sends the client a status message which the client prints out:

<img src="/images/posts/2017-10-01/socket_client_output.png" alt="client connects"/>

<h2>IP Packet Breakdown</h2>
You need to understand the innards of an IP packet in order to be able to successfully process it. If you recall the <a href="/notes/2017/06/01/osimodel/">OSI Model</a>, data sent over the network contains several layers of encapsulation. My Python script will be handling data being sent over layer 3 with IPv4 (Internet Protocol). An IP packet of this flavor can be broken into higher protocols/layers as shown:

<img src="/images/posts/2017-10-01/ip_packet_breakdown.png" alt="IP Packet Breakdown"/>

Here TCP is layer 4 (it could be swapped with UDP, another layer 4 protocol). The application layer is layer 7 in the OSI model.

<h3 style="font-size: 120%">IP Header</h3>
IPv4 handles fragmenting and reassembly packets as well as error reporting. Below shows the IP packet header:

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

Fields of particular interest to me here are the source and destination address. For a detailed explanation on the IP header and each of its fields checkout <a href="//www.rfc-base.org/txt/rfc-791.txt">RFC 791</a>.

<h3 style="font-size: 120%">TCP Header</h3>
While the IP header contains useful information, I can dig further into the layers to get more information. The next header in the packet is TCP, a connection-oriented layer 4 protocol. It's header is shown below: 

	 0                   1                   2                   3   
	 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |          Source Port          |       Destination Port        |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |                        Sequence Number                        |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |                    Acknowledgment Number                      |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |  Data |           |U|A|P|R|S|F|                               |
	 | Offset| Reserved  |R|C|S|S|Y|I|            Window             |
	 |       |           |G|K|H|T|N|N|                               |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |           Checksum            |         Urgent Pointer        |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |                    Options                    |    Padding    |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
	 |                             data                              |
	 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

For a detailed look at the TCP header and its fields checkout <a href="http://www.rfc-base.org/txt/rfc-793.txt">RFC 793</a>.

I am primarily interested in the ports in this header. I could dig even deeper into the packet to pull out more information. However, there are a lot of different protocols/applications that can be run on TCP. Handling all these variations can get pretty complicated. Frankly, there are already packet sniffers out there that handle processing these layers fairly well. So for this script I really only care about IP and TCP.

<h2>The Script</h2>
I was lucky to find a great tutorial for processing IP packets <a href="//www.binarytides.com/python-packet-sniffer-code-linux/">here</a>. I will walk though some of Silver Moon's examples since they helped me wrap my head around what I was doing. I encourage you to check out Silver Moon's tutorial as it covers additional socket configurations as well as Ethernet, IDP and ICMP headers which I haven't included in this post. My final script uses Silver Moon's processing algorithm (to convert hex into the header values). 

<h3 style="font-size: 120%">Capturing Raw Network Data</h3>
Below is a basic script for just capturing raw data being received your host.

	import socket

	# Create INET, RAW socket
	s = socket.socket(socket.AF_INET, socket.SOCKET_RAW, socket.IPPROTO_TCP)

	# Print Captured Packets
	while True:
		print s.recvfrom(80)

As you can see I currently have an apache webserver running on port 80:

<img src="/images/posts/2017-10-01/apache_port_80.png" alt="Apache Server is Running"/>

I can start my sniffer and then send a connection request to my apache webserver via netcat:

<img src="/images/posts/2017-10-01/nc_port_80.png" alt="Connect to Apache with netcat"/>

My packet sniffer then outputs the traffic it receives from the socket:

<img src="/images/posts/2017-10-01/raw_sniffer_output.png" alt="RAW Network Data"/>

You can see that at the moment this is just raw hex data. We need to process it to get any meaning out of it.

<h3 style="font-size: 120%">Processing the Data</h3>
Now that you have the raw data captured you can start parsing out data of interest from each header. You know the first two fields of the packet are the IP version (4 bits so the first hex digit) and IHL (IP header length) which is another 4 bits (or the next hex digit). Once you have the header length you can jump to the next IP header and begin the same process for parsing out the desired values there. Once you have the lengths of both headers (from the field values) you can grab "everything else" which per the TCP header is the "data" field.

Now when I run my packet sniffer and establish a connection with my apache server I get much more interesting output:

<img src="/images/posts/2017-10-01/parsed_network_packets.png" alt="Parsed Network Data"/>

The code for the packet sniffer script:

	import socket, sys
	from struct import *

	# create an INET raw socket
	try:
	    s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_TCP)
	except socket.error, msg:
	    print 'Socket could not be created. Error Code: ' + str(msg[0]) + ' Message ' + msg[1]
	    sys.exit()

	# receive a packet
	while True:
	    packet = s.recvfrom(80)

    #packet string from tuple
    packet = packet[0]

    #take first 20 characters for the IP header
    ip_header = packet[0:20]

    #now unpack them
    iph = unpack('!BBHHHBBH4s4s', ip_header)

    version_ihl = iph[0]
    version = version_ihl >> 4
    ihl = version_ihl & 0xF

    iph_length = ihl * 4

    ttl = iph[5]
    protocol = iph[6]
    s_addr = socket.inet_ntoa(iph[8])
    d_addr = socket.inet_ntoa(iph[9])

    print '-----------------------PACKET-----------------------'
    print "IP Header Info              | TCP Header Info"
    ip_info = ['Version: ' + str(version),
               'IP Header Length: ' + str(ihl), 
               'TTL: ' + str(ttl),
               'Protocol: ' + str(protocol), 
               'Source Address: ' + str(s_addr), 
               'Dest Address: ' + str(d_addr)]

    tcp_header = packet[iph_length:iph_length+20]

    #now unpack them
    tcph = unpack('!HHLLBBHHH', tcp_header)

    source_port = tcph[0]
    dest_port = tcph[1]
    sequence = tcph[2]
    acknowledgement = tcph[3]
    doff_reserved = tcph[4]
    tcph_length = doff_reserved >> 4

    tcp_info = ['Source Port: ' + str(source_port),
                'Destination Port: ' + str(dest_port),
                'Sequence: ' + str(sequence), 
                'Acknowledgement: ' + str(acknowledgement), 
                'TCP Header Length: ' + str(tcph_length)]

    h_size = iph_length + tcph_length * 4
    data_size = len(packet) - h_size

    #get data from the packet
    data = packet[h_size:]

    #print ip and tcp header info
    for i in range(0,len(ip_info)):
        tcp_data = ""
        if i < len(tcp_info):
            tcp_data = tcp_info[i]
        print ip_info[i] + " "*(28-len(ip_info[i])) + "| " + tcp_data

    print 'Data: ' + data
    print '-----------------------PACKET-----------------------'

You can also find it on <a href="https://github.com/pickl09/scripts/blob/master/packet_sniffer/packet_sniffer.py">github</a>.

<h2>References</h2>
**<a href="//www.binarytides.com/python-packet-sniffer-code-linux/">Basic Sniffer in Python</a>**<br/>
<a href="//www.geeksforgeeks.org/socket-programming-python/">Socket Programming in Python</a><br/>
<a href="//docs.python.org/2/library/socket.html">Python 2.7 Socket</a><br/>
<a href="//www.techrepublic.com/article/exploring-the-anatomy-of-a-data-packet/">Anatomy of a Data Packet</a><br/>
<a href="//nmap.org/book/tcpip-ref.html">NMAP TCP/IP Reference</a><br/>




