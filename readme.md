
# Table of Contents

1.  [Introduction](#org74ae550)
    1.  [Architecture](#org211ed05)
    2.  [Syntax](#orga02ffd2)
    3.  [Scan Techniques](#org05a4edc)
2.  [Host Discovery](#org73d0653)
    1.  [Scan Network Range](#orgad9825a)
    2.  [Scan IP List](#org07dcafc)
    3.  [Scan Multiple IPs](#org1878f1d)
    4.  [Scan Single IP](#org0919d14)
3.  [Host an Port Scanning](#org34ca7d2)
    1.  [Scanning Top 10 TCP Ports](#org8b5841a)
    2.  [Nmap - Trace the Packets](#orgf0ba561)
    3.  [Connect Scan](#org79bfdb0)
    4.  [Filtered Ports](#org97ef2d7)
    5.  [Discovering Open UDP Ports](#org76e74d3)
4.  [Saving the Results](#org66ee24d)
    1.  [Style sheets](#org9414865)
5.  [Service Enumeration](#org314c0cb)
    1.  [Service Version Detection](#orgee735ce)
    2.  [Banner Grabbing](#org0ffc276)
6.  [Nmap Scripting Engine](#org0e658e2)
    1.  [Default Scripts](#org3d4f183)
    2.  [Specific Scripts Category](#org70558b2)
    3.  [Defined Scripts](#org8d360bd)
    4.  [Nmap - Specifying Scripts](#orgd6093f4)
    5.  [Nmap - Scansione aggressiva](#org3a310c6)
    6.  [Vulnerability Assessment](#org61a0ac3)
        1.  [Nmap - Vuln Category](#org3d21e50)
7.  [Performance](#orgf66538e)
    1.  [Timeouts](#orgb284c89)
        1.  [Default Scan](#org284ad85)
        2.  [Optimized RTT](#orgd4d0d52)
    2.  [Max Retries](#org495dcb1)
        1.  [Default Scan](#org61fb324)
        2.  [Reduced Retries](#org5d7e82e)
    3.  [Rates](#orgd0c9b9d)
        1.  [Default Scan](#orga789978)
        2.  [Optimized Scan](#orgea1eec2)
        3.  [Default Scan - Found Open Ports](#orgdbd1aab)
        4.  [Optimized Scan - Found Open Ports](#org51cf301)
    4.  [Timing](#orga311dcd)
        1.  [Default Scan](#org338707c)
        2.  [Insane Scan](#org77bc8f1)
        3.  [Default Scan - Found Open Ports](#org5c04cf0)
        4.  [Insane Scan - Found Open Ports](#orgbe4bb17)
8.  [Firewall and IDS/IPS Evasion](#orgbf548d6)
    1.  [Determine Firewalls and Their Rules](#org98a45c4)
        1.  [Testing Firewall Rule](#org9282040)
    2.  [Detect IDS/IPS](#orga0b7081)
        1.  [IDS](#org221e8ea)
        2.  [IPS](#org0e3ff32)
        3.  [Decoys](#org113fbec)
    3.  [DNS Proxying](#org7c339e5)



<a id="org74ae550"></a>

# Introduction

Network Mapper (`Nmap`) is an open-source network analysis and security auditing tool written in C, C++, Python, and Lua. It is designed to scan networks and identify which hosts are available on the network using raw packets, and services and applications, including the name and version, where possible.


<a id="org211ed05"></a>

## Architecture

-   Host discovery
-   Port scanning
-   Service enumeration and detection
-   OS detection
-   Scriptable interaction with the target service (Nmap Scripting Engine)


<a id="orga02ffd2"></a>

## Syntax

The syntax for Nmap is fairly simple and looks like this:

    nmap <scan types> <options> <target>


<a id="org05a4edc"></a>

## Scan Techniques

Nmap offers many different scanning techniques, making different types of connections and using differently structured packets to send. Here we can see all the scanning techniques Nmap offers:

    nmap --help
    
    <SNIP>
    SCAN TECHNIQUES:
      -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
      -sU: UDP Scan
      -sN/sF/sX: TCP Null, FIN, and Xmas scans
      --scanflags <flags>: Customize TCP scan flags
      -sI <zombie host[:probeport]>: Idle scan
      -sY/sZ: SCTP INIT/COOKIE-ECHO scans
      -sO: IP protocol scan
      -b <FTP relay host>: FTP bounce scan
      <SNIP>


<a id="org73d0653"></a>

# Host Discovery


<a id="orgad9825a"></a>

## Scan Network Range

    sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left"><b>Scanning Options</b></th>
<th scope="col" class="org-left"><b>Description</b></th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><code>10.129.2.0/24</code></td>
<td class="org-left">Target network range.</td>
</tr>


<tr>
<td class="org-left"><code>-sn</code></td>
<td class="org-left">Disables port scanning.</td>
</tr>


<tr>
<td class="org-left"><code>-oA tnet</code></td>
<td class="org-left">Stores the results in all formats starting with the name 'tnet'.</td>
</tr>
</tbody>
</table>

This scanning method works only if the firewalls of the hosts allow it.


<a id="org07dcafc"></a>

## Scan IP List

List could look something like this:

    cat hosts.lst
    
    10.129.2.4
    10.129.2.10
    10.129.2.11
    10.129.2.18
    10.129.2.19
    10.129.2.20
    10.129.2.28

If we use the same scanning technique on the predefined list, the command will look like this:

    sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left"><b>Scanning Options</b></th>
<th scope="col" class="org-left"><b>Description</b></th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><code>-sn</code></td>
<td class="org-left">Disables port scanning.</td>
</tr>


<tr>
<td class="org-left"><code>-oA tnet</code></td>
<td class="org-left">Stores the results in all formats starting with the name 'tnet'.</td>
</tr>


<tr>
<td class="org-left"><code>-iL</code></td>
<td class="org-left">Performs defined scans against targets in provided 'hosts.lst' list.</td>
</tr>
</tbody>
</table>

In this example, we see that only 3 of 7 hosts are active. Remember, this may mean that the other hosts ignore the default ICMP echo requests because of their firewall configurations. Since Nmap does not receive a response, it marks those hosts as inactive.


<a id="org1878f1d"></a>

## Scan Multiple IPs

It can also happen that we only need to scan a small part of a network. An alternative to the method we used last time is to specify multiple IP addresses.

    sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20| grep for | cut -d" " -f5

If these IP addresses are next to each other, we can also define the range in the respective octet.

    sudo nmap -sn -oA tnet 10.129.2.18-20| grep for | cut -d" " -f5


<a id="org0919d14"></a>

## Scan Single IP

Before we scan a single host for open ports and its services, we first have to determine if it is alive or not.

    sudo nmap 10.129.2.18 -sn -oA host 

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left"><b>Scanning Options</b></th>
<th scope="col" class="org-left"><b>Description</b></th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><code>10.129.2.18</code></td>
<td class="org-left">Performs defined scans against the target.</td>
</tr>


<tr>
<td class="org-left"><code>-sn</code></td>
<td class="org-left">Disables port scanning.</td>
</tr>


<tr>
<td class="org-left"><code>-oA host</code></td>
<td class="org-left">Stores the results in all formats starting with the name 'host'.Stores the results in all formats starting with the name 'host'.</td>
</tr>

<tbody>
<tr>
</tr>
</tbody>
</table>

If we disable port scan (`-sn`), Nmap automatically ping scan with `ICMP Echo Requests` (`-PE`). Once such a request is sent, we usually expect an `ICMP reply` if the pinging host is alive. The more interesting fact is that our previous scans did not do that because before Nmap could send an ICMP echo request, it would send an `ARP ping` resulting in an `ARP reply`. We can confirm this with the "`--packet-trace`" option. To ensure that ICMP echo requests are sent, we also define the option (-PE) for this.

    sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left"><b>Scanning Options</b></th>
<th scope="col" class="org-left"><b>Description</b></th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><code>10.129.2.18</code></td>
<td class="org-left">Performs defined scans against the target.</td>
</tr>


<tr>
<td class="org-left"><code>-sn</code></td>
<td class="org-left">Disables port scanning.</td>
</tr>


<tr>
<td class="org-left"><code>-oA host</code></td>
<td class="org-left">Stores the results in all formats starting with the name 'host'.Stores the results in all formats starting with the name 'host'.</td>
</tr>


<tr>
<td class="org-left"><code>-PE</code></td>
<td class="org-left">Performs the ping scan by using 'ICMP Echo requests' against the target.</td>
</tr>


<tr>
<td class="org-left"><code>--packet-trace</code></td>
<td class="org-left">Shows all packets sent and received</td>
</tr>
</tbody>
</table>

Another way to determine why Nmap has our target marked as "`alive`" is with the "`--reason`" option.

    sudo nmap 10.129.2.18 -sn -oA host -PE --reason 

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left"><b>Scanning Options</b></th>
<th scope="col" class="org-left"><b>Description</b></th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><code>10.129.2.18</code></td>
<td class="org-left">Performs defined scans against the target.</td>
</tr>


<tr>
<td class="org-left"><code>-sn</code></td>
<td class="org-left">Disables port scanning.</td>
</tr>


<tr>
<td class="org-left"><code>-oA host</code></td>
<td class="org-left">Stores the results in all formats starting with the name 'host'.Stores the results in all formats starting with the name 'host'.</td>
</tr>


<tr>
<td class="org-left"><code>-PE</code></td>
<td class="org-left">Performs the ping scan by using 'ICMP Echo requests' against the target.</td>
</tr>


<tr>
<td class="org-left"><code>--reason</code></td>
<td class="org-left">Displays the reason for specific result.</td>
</tr>
</tbody>
</table>

We see here that `Nmap` does indeed detect whether the host is alive or not through the `ARP request` and `ARP reply` alone. To disable ARP requests and scan our target with the desired `ICMP echo requests`, we can disable ARP pings by setting the "`--disable-arp-ping`" option. Then we can scan our target again and look at the packets sent and received.

More strategies about host discovery can be found at:

<https://nmap.org/book/host-discovery-strategies.html>


<a id="org34ca7d2"></a>

# Host an Port Scanning


<a id="org8b5841a"></a>

## Scanning Top 10 TCP Ports

    sudo nmap 10.129.2.28 --top-ports=10 

`-sS` SYN scan
`-p <port>` define port to scan
`--top-ports=10` scan top 10 ports
`-p-` all port
`-F` top 100 ports


<a id="orgf0ba561"></a>

## Nmap - Trace the Packets

    sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping

`--packet-trace`  Shows all packets sent and received.
`-n`  Disables DNS resolution.
`--disable-arp-ping`  Disables ARP ping.


<a id="org79bfdb0"></a>

## Connect Scan

The Nmap TCP Connect Scan (`-sT`) uses the TCP **three-way handshake** to determine if a specific port on a target host is open or closed. The scan sends an `SYN` packet to the target port and waits for a response. It is considered open if the target port responds with an `SYN-ACK` packet and closed if it responds with an `RST` packet.

    sudo nmap 10.129.2.28 -p 443 --packet-trace --disable-arp-ping -Pn -n --reason -sT


<a id="org97ef2d7"></a>

## Filtered Ports

Let us look at an example where the firewall `drops` the TCP packets we send for the port scan. Therefore we scan the TCP port 139, which was already shown as filtered. To be able to track how our sent packets are handled, we deactivate the ICMP echo requests (`-Pn`), DNS resolution (`-n`), and ARP ping scan (`--disable-arp-ping`) again.

    sudo nmap 10.129.2.28 -p 139 --packet-trace -n --disable-arp-ping -Pn
    
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:45 CEST
    SENT (0.0381s) TCP 10.10.14.2:60277 > 10.129.2.28:139 S ttl=47 id=14523 iplen=44  seq=4175236769 win=1024 <mss 1460>
    SENT (1.0411s) TCP 10.10.14.2:60278 > 10.129.2.28:139 S ttl=45 id=7372 iplen=44  seq=4175171232 win=1024 <mss 1460>
    Nmap scan report for 10.129.2.28
    Host is up.
    
    PORT    STATE    SERVICE
    139/tcp filtered netbios-ssn
    MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
    
    Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds

We see in the last scan that `Nmap` sent two TCP packets with the SYN flag. By the duration (`2.06s`) of the scan, we can recognize that it took much longer than the previous ones (~0.05s). The case is different if the firewall rejects the packets. For this, we look at TCP port <span class="underline">445</span>, which is handled accordingly by such a rule of the firewall.

    sudo nmap 10.129.2.28 -p 445 --packet-trace -n --disable-arp-ping -Pn
    
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:55 CEST
    SENT (0.0388s) TCP 10.129.2.28:52472 > 10.129.2.28:445 S ttl=49 id=21763 iplen=44  seq=1418633433 win=1024 <mss 1460>
    RCVD (0.0487s) ICMP [10.129.2.28 > 10.129.2.28 Port 445 unreachable (type=3/code=3) ] IP [ttl=64 id=20998 iplen=72 ]
    Nmap scan report for 10.129.2.28
    Host is up (0.0099s latency).
    
    PORT    STATE    SERVICE
    445/tcp filtered microsoft-ds
    MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
    
    Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds

As a response, we receive an `ICMP` reply with `type 3` and error `code 3`, which indicates that the desired host is unreachable. Nevertheless, if we know that the host is alive, we can strongly assume that the firewall on this port is rejecting the packets, and we will have to take a closer look at this port later.


<a id="org76e74d3"></a>

## Discovering Open UDP Ports

Some system administrators sometimes forget to filter the UDP ports in addition to the TCP ones. Since `UDP` is a `stateless protocol` and does not require a three-way handshake like TCP. We do not receive any acknowledgment. Consequently, the timeout is much longer, making the whole `UDP scan` (`-sU`) much slower than the `TCP scan` (`-sS`).

    sudo nmap 10.129.2.28 -F -sU

Another disadvantage of this is that we often do not get a response back because Nmap sends empty datagrams to the scanned UDP ports, and we do not receive any response. So we cannot determine if the UDP packet has arrived at all or not. If the UDP port is `open`, we only get a response if the application is configured to do so.

    sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason 
    
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:15 CEST
    SENT (0.0367s) UDP 10.10.14.2:55478 > 10.129.2.28:137 ttl=57 id=9122 iplen=78
    RCVD (0.0398s) UDP 10.129.2.28:137 > 10.10.14.2:55478 ttl=64 id=13222 iplen=257
    Nmap scan report for 10.129.2.28
    Host is up, received user-set (0.0031s latency).
    
    PORT    STATE SERVICE    REASON
    137/udp open  netbios-ns udp-response ttl 64
    MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
    
    Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds

If we get an ICMP response with `error code 3` (port unreachable), we know that the port is indeed `closed`.

    sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 100 --reason 
    
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:25 CEST
    SENT (0.0445s) UDP 10.10.14.2:63825 > 10.129.2.28:100 ttl=57 id=29925 iplen=28
    RCVD (0.1498s) ICMP [10.129.2.28 > 10.10.14.2 Port unreachable (type=3/code=3) ] IP [ttl=64 id=11903 iplen=56 ]
    Nmap scan report for 10.129.2.28
    Host is up, received user-set (0.11s latency).
    
    PORT    STATE  SERVICE REASON
    100/udp closed unknown port-unreach ttl 64
    MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
    
    Nmap done: 1 IP address (1 host up) scanned in  0.15 seconds

For all other ICMP responses, the scanned ports are marked as (`open|filtered`).

    sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 138 --reason 
    
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:32 CEST
    SENT (0.0380s) UDP 10.10.14.2:52341 > 10.129.2.28:138 ttl=50 id=65159 iplen=28
    SENT (1.0392s) UDP 10.10.14.2:52342 > 10.129.2.28:138 ttl=40 id=24444 iplen=28
    Nmap scan report for 10.129.2.28
    Host is up, received user-set.
    
    PORT    STATE         SERVICE     REASON
    138/udp open|filtered netbios-dgm no-response
    MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
    
    Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds

Another handy method for scanning ports is the `-sV` option which is used to get additional available information from the open ports. This method can identify versions, service names, and details about our target.

    sudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason  -sV
    
    Starting Nmap 7.80 ( https://nmap.org ) at 2022-11-04 11:10 GMT
    SENT (0.3426s) TCP 10.10.14.2:44641 > 10.129.2.28:445 S ttl=55 id=43401 iplen=44  seq=3589068008 win=1024 <mss 1460>
    RCVD (0.3556s) TCP 10.129.2.28:445 > 10.10.14.2:44641 SA ttl=63 id=0 iplen=44  seq=2881527699 win=29200 <mss 1337>
    NSOCK INFO [0.4980s] nsock_iod_new2(): nsock_iod_new (IOD #1)
    NSOCK INFO [0.4980s] nsock_connect_tcp(): TCP connection requested to 10.129.2.28:445 (IOD #1) EID 8
    NSOCK INFO [0.5130s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.2.28:445]
    Service scan sending probe NULL to 10.129.2.28:445 (tcp)
    NSOCK INFO [0.5130s] nsock_read(): Read request from IOD #1 [10.129.2.28:445] (timeout: 6000ms) EID 18
    NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: READ TIMEOUT for EID 18 [10.129.2.28:445]
    Service scan sending probe SMBProgNeg to 10.129.2.28:445 (tcp)
    NSOCK INFO [6.5190s] nsock_write(): Write request for 168 bytes to IOD #1 EID 27 [10.129.2.28:445]
    NSOCK INFO [6.5190s] nsock_read(): Read request from IOD #1 [10.129.2.28:445] (timeout: 5000ms) EID 34
    NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: WRITE SUCCESS for EID 27 [10.129.2.28:445]
    NSOCK INFO [6.5320s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 34 [10.129.2.28:445] (135 bytes)
    Service scan match (Probe SMBProgNeg matched with SMBProgNeg line 13836): 10.129.2.28:445 is netbios-ssn.  Version: |Samba smbd|3.X - 4.X|workgroup: WORKGROUP|
    NSOCK INFO [6.5320s] nsock_iod_delete(): nsock_iod_delete (IOD #1)
    Nmap scan report for 10.129.2.28
    Host is up, received user-set (0.013s latency).
    
    PORT    STATE SERVICE     REASON         VERSION
    445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    Service Info: Host: Ubuntu
    
    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 6.55 secondssudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason  -sV
    
    Starting Nmap 7.80 ( https://nmap.org ) at 2022-11-04 11:10 GMT
    SENT (0.3426s) TCP 10.10.14.2:44641 > 10.129.2.28:445 S ttl=55 id=43401 iplen=44  seq=3589068008 win=1024 <mss 1460>
    RCVD (0.3556s) TCP 10.129.2.28:445 > 10.10.14.2:44641 SA ttl=63 id=0 iplen=44  seq=2881527699 win=29200 <mss 1337>
    NSOCK INFO [0.4980s] nsock_iod_new2(): nsock_iod_new (IOD #1)
    NSOCK INFO [0.4980s] nsock_connect_tcp(): TCP connection requested to 10.129.2.28:445 (IOD #1) EID 8
    NSOCK INFO [0.5130s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.2.28:445]
    Service scan sending probe NULL to 10.129.2.28:445 (tcp)
    NSOCK INFO [0.5130s] nsock_read(): Read request from IOD #1 [10.129.2.28:445] (timeout: 6000ms) EID 18
    NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: READ TIMEOUT for EID 18 [10.129.2.28:445]
    Service scan sending probe SMBProgNeg to 10.129.2.28:445 (tcp)
    NSOCK INFO [6.5190s] nsock_write(): Write request for 168 bytes to IOD #1 EID 27 [10.129.2.28:445]
    NSOCK INFO [6.5190s] nsock_read(): Read request from IOD #1 [10.129.2.28:445] (timeout: 5000ms) EID 34
    NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: WRITE SUCCESS for EID 27 [10.129.2.28:445]
    NSOCK INFO [6.5320s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 34 [10.129.2.28:445] (135 bytes)
    Service scan match (Probe SMBProgNeg matched with SMBProgNeg line 13836): 10.129.2.28:445 is netbios-ssn.  Version: |Samba smbd|3.X - 4.X|workgroup: WORKGROUP|
    NSOCK INFO [6.5320s] nsock_iod_delete(): nsock_iod_delete (IOD #1)
    Nmap scan report for 10.129.2.28
    Host is up, received user-set (0.013s latency).
    
    PORT    STATE SERVICE     REASON         VERSION
    445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    Service Info: Host: Ubuntu
    
    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 6.55 seconds


<a id="org66ee24d"></a>

# Saving the Results

`Nmap` can save the results in 3 different formats.

-   Normal output (`-oN`) with the `.nmap` file extension
-   Grepable output (`-oG`) with the `.gnmap` file extension
-   XML output (`-oX`) with the `.xml` file

We can also specify the option (`-oA`) to save the results in all formats. The command could look like this:

    sudo nmap 10.129.2.28 -p- -oA target

`-oA target` => Saves the results in all formats, starting the name of each file with 'target'.


<a id="org9414865"></a>

## Style sheets

With the XML output, we can easily create HTML reports that are easy to read, even for non-technical people.
To convert the stored results from XML format to HTML, we can use the tool `xsltproc`.

    xsltproc target.xml -o target.html


<a id="org314c0cb"></a>

# Service Enumeration


<a id="orgee735ce"></a>

## Service Version Detection

A full port scan takes quite a long time. To view the scan status, we can press the `[Space Bar]` during the scan, which will cause `Nmap` to show us the scan status.

    sudo nmap 10.129.2.28 -p- -sV

Another option (`--stats-every=5s`) that we can use is defining how periods of time the status should be shown. Here we can specify the number of seconds (`s`) or minutes (`m`), after which we want to get the status.

    sudo nmap 10.129.2.28 -p- -sV --stats-every=5s

`--stats-every=5s` => Shows the progress of the scan every 5 seconds.

We can also increase the `verbosity level` (`-v` / `-vv`), which will show us the open ports directly when `Nmap` detects them.

    sudo nmap 10.129.2.28 -p- -sV -v 

`-v` Increases the verbosity of the scan, which displays more detailed information.


<a id="org0ffc276"></a>

## Banner Grabbing

Once the scan is complete, we will see all TCP ports with the corresponding service and their versions that are active on the system.

    sudo nmap 10.129.2.28 -p- -sV

Primarily, Nmap looks at the banners of the scanned ports and prints them out. If it cannot identify versions through the banners, Nmap attempts to identify them through a signature-based matching system, but this significantly increases the scan's duration. One disadvantage to Nmap's presented results is that the automatic scan can miss some information because sometimes Nmap does not know how to handle it. Let us look at an example of this.

    sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-trace

`-p-` => Scans all ports.
`-sV` => Performs service version detection on specified ports.
`-Pn` => Disables ICMP Echo requests.
`-n` => Disables DNS resolution.
`--disable-arp-ping` => Disables ARP ping.
`--packet-trace` => Shows all packets sent and received.

However, it can happen that some services do not immediately provide such information. It is also possible to remove or manipulate the banners from the respective services. If we `manually` connect to the SMTP server using `nc`, grab the banner, and intercept the network traffic using `tcpdump`, we can see what `Nmap` did not show us.

<span class="underline">Tcpdump</span>

    sudo tcpdump -i eth0 host 10.10.14.2 and 10.129.2.28

<span class="underline">Nc</span>

    nc -nv 10.129.2.28 25

<span class="underline">Tcpdump - Intercepted Traffic</span>

    18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], seq 1798872233, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 331260178 ecr 0,sackOK,eol], length 0
    18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], seq 1130574379, ack 1798872234, win 65160, options [mss 1460,sackOK,TS val 1800383922 ecr 331260178,nop,wscale 7], length 0
    18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 1, win 2058, options [nop,nop,TS val 331260304 ecr 1800383922], length 0
    18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], seq 1:36, ack 1, win 510, options [nop,nop,TS val 1800383985 ecr 331260304], length 35: SMTP: 220 inlane ESMTP Postfix (Ubuntu)


<a id="org0e658e2"></a>

# Nmap Scripting Engine

Nmap Scripting Engine (`NSE`) is another handy feature of `Nmap`. It provides us with the possibility to create scripts in Lua for interaction with certain services. There are a total of 14 categories into which these scripts can be divided:

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left"><b>Category</b></th>
<th scope="col" class="org-left"><b>Description</b></th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><code>auth</code></td>
<td class="org-left">Determination of authentication credentials.</td>
</tr>


<tr>
<td class="org-left"><code>brodcast</code></td>
<td class="org-left">Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans.</td>
</tr>


<tr>
<td class="org-left"><code>brute</code></td>
<td class="org-left">Executes scripts that try to log in to the respective service by brute-forcing with credentials.</td>
</tr>


<tr>
<td class="org-left"><code>default</code></td>
<td class="org-left">Default scripts executed by using the <code>-sC</code> option.</td>
</tr>


<tr>
<td class="org-left"><code>discovery</code></td>
<td class="org-left">Evaluation of accessible services.</td>
</tr>


<tr>
<td class="org-left"><code>dos</code></td>
<td class="org-left">These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.</td>
</tr>


<tr>
<td class="org-left"><code>exploit</code></td>
<td class="org-left">This category of scripts tries to exploit known vulnerabilities for the scanned port.</td>
</tr>


<tr>
<td class="org-left"><code>external</code></td>
<td class="org-left">Scripts that use external services for further processing.</td>
</tr>


<tr>
<td class="org-left"><code>fuzzer</code></td>
<td class="org-left">This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.</td>
</tr>


<tr>
<td class="org-left"><code>intrusive</code></td>
<td class="org-left">Intrusive scripts that could negatively affect the target system.</td>
</tr>


<tr>
<td class="org-left"><code>malware</code></td>
<td class="org-left">Checks if some malware infects the target system.</td>
</tr>


<tr>
<td class="org-left"><code>safe</code></td>
<td class="org-left">Defensive scripts that do not perform intrusive and destructive access.</td>
</tr>


<tr>
<td class="org-left"><code>version</code></td>
<td class="org-left">Extension for service detection.</td>
</tr>


<tr>
<td class="org-left"><code>vuln</code></td>
<td class="org-left">Identification of specific vulnerabilities.</td>
</tr>
</tbody>
</table>


<a id="org3d4f183"></a>

## Default Scripts

    sudo nmap <target> -sC


<a id="org70558b2"></a>

## Specific Scripts Category

    sudo nmap <target> --script <category>


<a id="org8d360bd"></a>

## Defined Scripts

    sudo nmap <target> --script <script-name>,<script-name>,...


<a id="orgd6093f4"></a>

## Nmap - Specifying Scripts

    sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands

`--script banner,smtp-commands` => Uses specified NSE scripts.


<a id="org3a310c6"></a>

## Nmap - Scansione aggressiva

    sudo nmap 10.129.2.28 -p 80 -A


<a id="org61a0ac3"></a>

## Vulnerability Assessment

Now let us move on to HTTP port 80 and see what information and vulnerabilities we can find using the `vuln` category from `NSE`.


<a id="org3d21e50"></a>

### Nmap - Vuln Category

    sudo nmap 10.129.2.28 -p 80 -sV --script vuln


<a id="orgf66538e"></a>

# Performance

Scanning performance plays a significant role when we need to scan an extensive network or are dealing with low network bandwidth. We can use various options to tell `Nmap` how fast (`-T <0-5>`), with which frequency (`--min-parallelism <number>`), which timeouts (`--max-rtt-timeout <time>`) the test packets should have, how many packets should be sent simultaneously (`--min-rate <number>`), and with the number of retries (`--max-retries <number>`) for the scanned ports the targets should be scanned.


<a id="orgb284c89"></a>

## Timeouts

When Nmap sends a packet, it takes some time (`Round-Trip-Time - RTT`) to receive a response from the scanned port. Generally, `Nmap` starts with a high timeout (`--min-RTT-timeout`) of 100ms. 


<a id="org284ad85"></a>

### Default Scan

    sudo nmap 10.129.2.0/24 -F


<a id="orgd4d0d52"></a>

### Optimized RTT

    sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms

`-F` => Scans top 100 ports.
`--initial-rtt-timeout 50ms` => Sets the specified time value as initial RTT timeout.
`--max-rtt-timeout 100ms` => Sets the specified time value as maximum RTT timeout.


<a id="org495dcb1"></a>

## Max Retries

Another way to increase the scans' speed is to specify the retry rate of the sent packets (`--max-retries`). The default value for the retry rate is `10`, so if `Nmap` does not receive a response for a port, it will not send any more packets to the port and will be skipped.


<a id="org61fb324"></a>

### Default Scan

    sudo nmap 10.129.2.0/24 -F | grep "/tcp" | wc -l


<a id="org5d7e82e"></a>

### Reduced Retries

    sudo nmap 10.129.2.0/24 -F --max-retries 0 | grep "/tcp" | wc -l

`--max-retries 0` => Sets the number of retries that will be performed during the scan.


<a id="orgd0c9b9d"></a>

## Rates

During a white-box penetration test, we may get whitelisted for the security systems to check the systems in the network for vulnerabilities and not only test the protection measures. If we know the network bandwidth, we can work with the rate of packets sent, which significantly speeds up our scans with `Nmap`. When setting the minimum rate (`--min-rate <number>`) for sending packets, we tell `Nmap` to simultaneously send the specified number of packets. It will attempt to maintain the rate accordingly.


<a id="orga789978"></a>

### Default Scan

    sudo nmap 10.129.2.0/24 -F -oN tnet.default


<a id="orgea1eec2"></a>

### Optimized Scan

    sudo nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300

`-oN tnet.minrate 300` => Saves the results in normal formats, starting the specified file name.
`--min-rate 300` => Sets the minimum number of packets to be sent per second.


<a id="orgdbd1aab"></a>

### Default Scan - Found Open Ports

    cat tnet.default | grep "/tcp" | wc -l


<a id="org51cf301"></a>

### Optimized Scan - Found Open Ports

    cat tnet.minrate300 | grep "/tcp" | wc -l


<a id="orga311dcd"></a>

## Timing

Because such settings cannot always be optimized manually, as in a black-box penetration test, `Nmap` offers six different timing templates (`-T <0-5>`) for us to use. These values (`0-5`) determine the aggressiveness of our scans.

-   `-T 0` / `-T paranoid`
-   `-T 1` / `-T sneaky`
-   `-T 2` / `-T polite`
-   `-T 3` / `-T normal`
-   `-T 4` / `-T aggressive`
-   `-T 5` / `-T insane`


<a id="org338707c"></a>

### Default Scan

    sudo nmap 10.129.2.0/24 -F -oN tnet.default 


<a id="org77bc8f1"></a>

### Insane Scan

    sudo nmap 10.129.2.0/24 -F -oN tnet.T5 -T 5

`-oN tnet.T5` => Saves the results in normal formats, starting the specified file name.
`-T 5` => Specifies the insane timing template.


<a id="org5c04cf0"></a>

### Default Scan - Found Open Ports

    cat tnet.default | grep "/tcp" | wc -l


<a id="orgbe4bb17"></a>

### Insane Scan - Found Open Ports

    cat tnet.T5 | grep "/tcp" | wc -l


<a id="orgbf548d6"></a>

# Firewall and IDS/IPS Evasion

`Nmap` has tricks to sneak past `firewalls` and `IDS` / `IPS`. For example, it can send packets in tiny pieces or pretend to be someone else.


<a id="org98a45c4"></a>

## Determine Firewalls and Their Rules

We already know that when a port is shown as filtered, it can have several reasons. In most cases, firewalls have certain rules set to handle specific connections. The packets can either be `dropped`, or `rejected`. The `dropped` packets are ignored, and no response is returned from the host.

This is different for `rejected` packets that are returned with an `RST` flag. These packets contain different types of ICMP error codes or contain nothing at all.
<span class="underline">SYN-Scan</span>

    sudo nmap 10.129.2.28 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace

<span class="underline">ACK-Scan</span>

    sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace

`-sS` Performs SYN scan on specified ports.
`-sA` Performs ACK scan on specified ports.
`-Pn` Disables ICMP Echo requests.
`-n` Disables DNS resolution.
`--disable-arp-ping` Disables ARP ping.
`--packet-trace` Shows all packets sent and received.


<a id="org9282040"></a>

### Testing Firewall Rule

    sudo nmap 10.129.2.28 -n -Pn -p445 -O

<span class="underline">Scan by Using Different Source IP</span>

    sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0


<a id="orga0b7081"></a>

## Detect IDS/IPS

Unlike firewalls and their rules, the detection of IDS/IPS systems is much more difficult because these are passive traffic monitoring systems


<a id="org221e8ea"></a>

### IDS

`IDS systems` examine all connections between hosts. If the IDS finds packets containing the defined contents or specifications, the administrator is notified and takes appropriate action in the worst case.

IDS systems alone are usually there to help administrators detect potential attacks on their network. They can then decide how to handle such connections. We can trigger certain security measures from an administrator, for example, by aggressively scanning a single port and its service. Based on whether specific security measures are taken, we can detect if the network has some monitoring applications or not.


<a id="org0e3ff32"></a>

### IPS

One method to determine whether such `IPS system` is present in the target network is to scan from a single host (`VPS`). If at any time this host is blocked and has no access to the target network, we know that the administrator has taken some security measures. Accordingly, we can continue our penetration test with another `VPS`.


<a id="org113fbec"></a>

### Decoys

Decoy scanning method (-D) is the right choice.
With this method, Nmap generates various random IP addresses inserted into the IP header to disguise the origin of the packet sent.

`RND` value for specific number of IP address separated by a colon (`:`).
<span class="underline">Scan by Using Decoys</span>

    sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5

`-sS` Performs SYN scan on specified ports.
`-Pn` Disables ICMP Echo requests.
`-n` Disables DNS resolution.
`--disable-arp-ping` Disables ARP ping.
`--packet-trace` Shows all packets sent and received.
`-D RND:5` Generates five random IP addresses that indicates the source IP the connection comes from.

The spoofed packets are often filtered out by ISPs and routers, even though they come from the same network range. Therefore, we can also specify our VPS servers' IP addresses and use them in combination with "IP ID" manipulation in the IP headers to scan the target.

Another scenario would be that only individual subnets would not have access to the server's specific services. So we can also manually specify the source IP address (`-S`) to test if we get better results with this one. Decoys can be used for SYN, ACK, ICMP scans, and OS detection scans.

    sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0


<a id="org7c339e5"></a>

## DNS Proxying

By default, `Nmap` performs a reverse DNS resolution unless otherwise specified to find more important information about our target. These DNS queries are also passed in most cases because the given web server is supposed to be found and visited. The DNS queries are made over the `UDP port 53`. The `TCP port 53` was previously only used for the so-called "`Zone transfers`" between the DNS servers or data transfer larger than 512 bytes. More and more, this is changing due to IPv6 and DNSSEC expansions. These changes cause many DNS requests to be made via TCP port 53.

However, `Nmap` still gives us a way to specify DNS servers ourselves (`--dns-server <ns>,<ns>`). This method could be fundamental to us if we are in a demilitarized zone (`DMZ`). The company's DNS servers are usually more trusted than those from the Internet. So, for example, we could use them to interact with the hosts of the internal network. As another example, we can use `TCP port 53` as a source port (`--source-port`) for our scans. If the administrator uses the firewall to control this port and does not filter IDS/IPS properly, our TCP packets will be trusted and passed through.

<span class="underline">SYN-Scan of a Filtered Port</span>

    sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace

<span class="underline">SYN-Scan From DNS Port</span>

    sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53

Now that we have found out that the firewall accepts `TCP port 53`, it is very likely that IDS/IPS filters might also be configured much weaker than others. We can test this by trying to connect to this port by using `Netcat`.

<span class="underline">Connect To The Filtered Port</span>

    ncat -nv --source-port 53 10.129.2.28 50000

