---
layout: note
type: Note
category: Notes
tags: [networking, tcp/ip]
title: TCP/IP Networking Protocols
---

This page is intended as a quick reference for TCP/IP protocols. For a quick reference to the layers of the OSI model see <a href="/notes/2017/06/01/osimodel/">here</a>. These layer 5 to 7 protocols run on TCP and/or UDP (layer 4 protocols) and are each associated with a virtual port number between 0 and 65,535 (note that TCP and UDP do not share these numbers, 80 TCP and 80 UDP are completely different ports on a computer). The port number tells the computer what service or application running on that computer should handle the message it just received. The port number is usually determined by the protocol the service is using to communicate with other devices on the network. 

There are ephemeral and non-ephemeral port numbers. Ephemeral port numbers are are determined in real-time to establish temporary communication with a server (like when a client accesses a web service). Non-ephemeral port numbers are permanent, meaning that application or protocol should always be using that port. Most services have non-ephemeral ports associated with them; however, the default port can be overridden, causing that data to be sent to a different port from the default. This works fine as long as all clients communicating with that service on the server know what the port number for that service should be.

<h2>TCP/IP Protocols**</h2>
<table>
  <tr style="font-weight: bold;"><td>Protocols</td><td>TCP/UDP</td><td>Port</td><td>Full Name</td></tr>

  <tr><td>FTP</td><td>TCP</td><td>20/21</td><td>File Transfer Protocol</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">Common file transfer protocol that allows users to move files between devices. All files are passed in the clear, does not support encryption.
  <ul><li>21 - control, sets up connection via this port</li><li>20 - actual active data transfer occurs on port 20</li></ul></td></tr>

  <tr><td>SSH / SCP / SFTP</td><td>TCP</td><td>22</td><td>Secure Shell / Secure Copy / SSH File Transfer Protocol</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">Every admin/developer's favorite protocol. Allows you to connect remotely to devices over an encrypted communications link. Enable it wisely.<br/>
  SSH supports SCP, a barebones file transfer process that uses SSH to securely transfer files.<br/>
  SSH also supports SFTP which offers more functionality than SCP (such as directory listings, remote file removal, file transfer interrupts, etc.).</td></tr>

  <tr><td>Telnet</td><td>TCP</td><td>23</td><td>Telecommunication Network</td></tr>
   <tr><td></td><td colspan="3" style="width:100%">Every hacker's favorite protocol. Allows you to remotely connect to devices via console window. There are no secure versions of telnet. It is a legacy protocol that should be blocked. If it's running on a device that is meant to be open to everyone and everything, ensure that that device is isolated properly from the rest of your network.</td></tr>

  <tr><td>SMTP</td><td>TCP</td><td>25</td><td>Simple Mail Transfer Protocol</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">Used by mail clients to retrieve mail.</td></tr>

  <tr><td>DNS</td><td>TCP/UDP</td><td>53</td><td>Domain Name Services</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">Converts names to IP addresses. Critical resource that is susceptible to DoS, phishing or redirection attacks which could cripple the network.
  <ul><li>53 tcp - used for zone transfers</li>
  <li>53 udp - used for queries (name services lookup)</li>
  </ul></td></tr>

  <tr><td>DHCP</td><td>UDP</td><td>67/68</td><td>Dynamic Host Configuration Protocol</td></tr>
   <tr><td></td><td colspan="3" style="width:100%"></td></tr>

  <tr><td>TFTP</td><td>UDP</td><td>69</td><td>Trivial File Transfer Protocol</td></tr>
   <tr><td></td><td colspan="3" style="width:100%"></td></tr>

  <tr><td>HTTP</td><td>TCP</td><td>80</td><td>Hypertext Transfer Protocol</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">The Internet -> communications between client browsers and web servers.</td></tr>

  <tr><td>POP3</td><td>TCP</td><td>110</td><td>Post Office Protocol version 3</td></tr>
   <tr><td></td><td colspan="3" style="width:100%">Used by mail clients to retrieve mail.</td></tr>

  <tr><td>NTP</td><td>UDP</td><td>123</td><td>Network Time Protocol</td></tr>
   <tr><td></td><td colspan="3" style="width:100%"></td></tr>

  <tr><td>NetBIOS / NBT</td><td>TCP/UDP</td><td>137/138/139</td><td>Network Basic Input/Output System / NetBIOS over TCP/IP</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">Used for communication between Windows devices:
  <ul><li>Name service - udp/137, tcp/137</li>
  <li>Datagram service - udp/138 (connectionless)</li>
  <li>Session service - tcp/139 (connection service)</li></ul>Windows hosts use NetBIOS for all kinds things, from announcing itself to other Windows devices on the network to file transfers, etc.</td></tr>

  <tr><td>IMAP</td><td>TCP</td><td>143</td><td>Internet Message Access Protocol</td></tr>
   <tr><td></td><td colspan="3" style="width:100%"></td></tr>

  <tr><td>SNMP</td><td>TCP/UDP</td><td>161/162</td><td>Simple Network Management Protocol</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">Used to gather data on how devices are performing (bandwidth, temperature, current # of users). There are currently 3 versions available. Versions 1 and 2 have no encryption (everything is sent in plaintext) so you should always use SNMPv3 or higher (has encryption and authentication) if you have the choice. Access to SNMP should be limited!</td></tr>

  <tr><td>BGP</td><td>TCP</td><td>179</td><td>Border Gateway Protocol</td></tr>
   <tr><td></td><td colspan="3" style="width:100%"></td></tr>

  <tr><td>LDAP</td><td>TCP/UDP</td><td>389</td><td>Lightweight Directory Access Protocol</td></tr>
   <tr><td></td><td colspan="3" style="width:100%"></td></tr>

  <tr><td>HTTPS</td><td>TCP</td><td>443</td><td>Hypertext Transfer Protocol over SSL/TLS or Hypertext Transfer Protocol Secure</td></tr>
   <tr><td></td><td colspan="3" style="width:100%">HTTP with an extra layer of encryption via TLS/SSL (Transport Layer Security / Secure Sockets Layer).</td></tr>

  <tr><td>LDAPS</td><td>TCP/UDP</td><td>636</td><td>Lightweight Directory Access Protocol over TLS/SSL</td></tr>
   <tr><td></td><td colspan="3" style="width:100%"></td></tr>

  <tr><td>FTPS</td><td>TCP</td><td>989/990</td><td>FTP over TLS/SSL or File Transfer Protocol Secure</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">The secure version of FTP, supports encryption using SSL. FTPS is commonly used on web servers. NOTE: This is NOT the same as SFTP which is built on top of SSH. </td></tr>

   <tr><td>RDP</td><td>TCP</td><td>3389</td><td>Remote Desktop Protocol</td></tr>
  <tr><td></td><td colspan="3" style="width:100%">Allows you to view the contents of the desktop of a remote device.</td></tr>

</table>
<span style="font-size: 80%">**For a comprehensive list of protocol assignments check out the <a href="//www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xml">IANA website</a>.</span><br/>
<span style="font-size: 80%">**Another great quick reference can be found <a href="//packetlife.net/media/library/23/common_ports.pdf">here</a>.</span>



