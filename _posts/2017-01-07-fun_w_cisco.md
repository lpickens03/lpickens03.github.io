---
layout: note
type: Notes
title: Configuring Cisco Switches
---

This weekend as part of my ongoing endeavor to get my home lab up and running, I tackled my first proper Cisco switch. Two Cisco Catalyst 3550 switches had been donated to my cause a few months back and I finally had the time to configure them today.

<img src="/images/posts/2017-01-07/cat3550s.bmp" alt="Cisco Catalyst 3550s"/>

I was starting from square one with familiarizing myself with the 3550s' CLI. The switches had previously been used in a retail store of some sort so there was the added fun of restoring each switch to factory settings. At the end of the day, my goal was to break up the 48 FastEthernet ports into several vlans in prep for being connected up to my home lab network.

<br/>
<h2>Connecting to the Switch</h2>
One of the first things I realized I needed was a console cable. I had no idea what IP address the switch was currently set to, so attaching an ethernet cable and attempting to telnet in would do me no good. Since my MacBook supports limited ports, I used this <a href="//www.amazon.com/gp/product/B00I8CT8YG/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1">USB to DB9 Cisco Console Cable</a> shown below.

<img src="/images/posts/2017-01-07/control_cable.bmp" alt="USB to DB9 Cisco Console Cable"/>

Once my Mac and the switch were connected, I just had to find the associated usb device under /dev and run the following command:

<code> screen /dev/tty.usbserial-AL00B2R5 9600 </code>

Note in the above the 9600 refers to the baud rate of the console port which can be found in the documentation for the switch.

<br/>
<h2>Back to Factory Defaults</h2>
This successfully brought up a connection with the switch. Unfortunately, it became clear that there wasn't much I could do without the password to allow me to edit the switch configuration. While I could attempt to recover the password, since I had no need to save the original switch config, I opted to restore it to factory defaults.

To do this, I simply unplugged the switch, held down the mode button on the front and plugged it in again. The only snag I ran into here, which admittedly took me a while to figure out, is that I needed to unplug the console cable before I turned the switch back on from both the switch and the Mac. Otherwise, when I tried to connect to the switch with the console cable, I'd either get a busy signal or a blank screen. Once this was sorted though, I successfully brought up the switch prompt.

I then followed Cisco's instructions found <a href="//www.cisco.com/image/gif/paws/12040/pswdrec_2900xl.pdf">here</a>.

I used the following commands to initialize the flash file system:

<code>switch: flash_init</code>

<code>switch: load_helper</code>

I then found the original configuration file and renamed it:

<code>switch: dir flash:</code>

<code>switch: rename flash:config.text config.old</code>

Finally, I booted up the switch:

<code>switch: boot</code>

<br/>
<h2>Initial Configuration</h2>
Upon reboot, it enters initial configuration mode. I hit "<strong>n</strong>" to abort this and got the default Switch prompt. I then entered "<strong>en</strong>" to enter "enable" mode or what I think of as admin mode and voila! No password required and I am now able to set up the switch as I like. For the start, I followed <a href="//www.youtube.com/watch?v=n_3CHv9bXdc">this tutorial</a> I found on youtube. The poster, <a href="//www.youtube.com/channel/UCV0w1MlGfnPe9kR1jmBjGLQ"> Jorge Almazan</a>, included a good set of "recommended settings" which I will paraphrase in the following sections.

<br/>
<h2>Configuring the Switch</h2>
Before diving into the details of the configuration, a quick overview of how the switch config if updated and saved. 

In order to configure the switch you need to enter configuration mode. To enter configuration mode you run the following command:

<code>configure terminal</code>

To exit configuration mode, you enter:

<code>end</code>

All of the updates you are making to the configuration are only saved into the switch's running configuration. If you rebooted the switch at any point, it would load the starting configuration and any updates you made to it's running configuration would be lost. Therefore, you should frequently save your changes as you go with this command run in "enable" mode:

<code>copy running-config startup-config</code>

The following sections assume you are entering and leaving configuration mode appropriately and saving your configuration frequently along the way.

<br/>
<h2>Security</h2>
There are a number of items you need to configure initially. First and foremost, are the logins and passwords. The first password you want to set is for "enable" mode, so basically your root password. You set this as follows:

<code>enable secret &lt;your password&gt;</code>

You also want to set a password on your switch's console so that whenever someone logs into the switch they are prompted for a password. To configure the console's settings you enter:

<code>line console 0</code>

Once run, the following commands you run are applied only to the console. Here we want to set the console's password, require users to login and set a timeout period to automatically log the user out after a number of minutes and/or seconds.

<code>password &lt;your password&gt;</code>

<code>login</code>

<code>exec-timeout 30 0</code>

While inside the console's configuration, there are also some additional settings you can make to improve your interface with the console. One I highly recommend prevents the command line from being split in two by log messages:

<code>logging synchronous</code>

To leave the console configuration mode, you run:

<code>exit</code>

As the line name implies, this password is only for the console reached via the console cable we are currently using. Normally, once the switch is configured, you'd be using either <strong>telnet</strong> or, preferably from a security standpoint, <strong>ssh</strong> to access the switch remotely over the network. The 3550s allow you to set access controls for remote users with virtual terminal lines, or "vty". My 3550s support up to 16 simultaneous connections so the command to enter the vty configuration mode for all connections would be

<code>line vty 0 15</code>

With this command, you are now in vty's configuration. All the settings applied to the console's configuration should also be applied here.

Unfortunately, the default settings on the switch store these passwords in plain text. If you run 

<code>show running-config</code>

in "enable mode", you will see the passwords crystal clear listed in the configuration. To encrypt the passwords where they are stored, you will want to run 

<code>service password encryption</code>

Now if you check the running config, a hash of the password is shown to mask it from prying eyes. Unfortunately, the hashing functions that these switches use are not strong and with minimal effort the passwords can be derived from the hash. I take a deeper look into the Catalyst's weak hashing functions in <a href="/2017/01/08/cisco_type_7/">this post</a>.

<br/>
<h2>Miscellaneous Settings</h2>
To set the login banner:

<code>banner motd [</code>

To set the hostname of the switch:

<code>hostname &lt;your switch's name&gt;</code>

<br/>
<h2>VLANs</h2>
The default vlan used by all of the switch's ports is vlan 1. I need to break the switch up into multiple vlans to support my network's segregation.

First I need to create the new vlans:

<code>vlan &lt;vlan #&gt;</code>

<code>name &lt;name of vlan (for your reference)&gt;</code>

<code>exit</code>

I then need to assign each port to a vlan. I could do this for each port individually, but, fortunately, the 3550s allow you to assign a configure a range of interfaces at once. In the below commands x represents the first ethernet port in the range and y represents the last port. You can find the port numbers on the physical ports or get a list with the "<strong>show ip interface brief</strong>" command.

<code>interface range fastethernet 0/x – y</code>

<code>switchport mode access</code>

<code>switchport access vlan &lt;vlan #&gt;</code>

<code>exit</code>

To set an IP address range to your vlan, you have to add interfaces for each of your vlans. Once you create the interface, you then add your ip address of choice and the subnet mask to dictate the range of ip addresses that will be on that vlan.

<code>interface vlan &lt;vlan #&gt;</code>

<code>ip address 10.0.0.1 255.255.255.0</code>

With the ip address set for the vlan, you can connect a device to one of the ports, set the device's IP address within that IP address range and then ping the switch on the IP address you set for the vlan that port is using. 

All of this is just the tip of the iceberg as far as switch configuration goes and fully integrating these switches into my lab's network. I look forward to posting additional updates as I learn more about these switches and how best to use them.



