# Introduction to traffic sniffing

So we've gathered some packet information about a target. By dissecting the protocols and raw traffic, we can determine exactly what is happening on a network. Ultimately, this is all about protocol analysis.

## Sniffing Traffic

## Concepts

-   Hardware / Software

-   SPAN (switch port analysis)

    -   port duplicating

    -   configured to listen to traffic between source and destination

    -   IPv4 and IPv6 protocols

-   Promiscuous mode

    -   turns network interface into traffic sniffer

-   hubs vs switches

    -   hubs are accessible by the entire network

    -   switches only interact with the source and destination

        -   possible to introduce a collision domain

    -   all broadcast traffic is viewable on both

-   protocols

    -   dealing with raw protocols

## Threats

-   disclosure

-   depends

-   email

-   web Traffic

-   chat

-   router updates

-   telnet (old, but sometimes still in use)

-   **cleartext**

    -   some application specific password encryptions transmit passwords in clear text when moving from one application to another

## Wiretapping

-   MAC spoof

    -   clone a MAC address or manipulate the ARP process

-   DHCP starvation

    -   could grab a list of all of the available DHCP addresses from the server

    -   attacks on availability can prevent legitimate users from obtaining a DHCP address

-   ARP spoofing

    -   posing as another device in order to coerce switches into transmitting a copy of traffic to us

-   MAC duplication

-   MAC flooding

## Protocols

DHCP

-   discover

-   offer

-   request

-   offer

MAC/ARP analysis

-   how CAM tables work

    -   computer advertises through switch

    -   switch looks at sources

    -   switch analyzes MAC address

    -   switch records the address

    -   switch maps address to a particular port

    -   reply comes back from other computer

    -   table is built

    -   **Open to traffic duplication vulnerabilities (disclosure)**

        -   traffic is advertised to host `C` on a network

        -   request is made to send another copy to host `D`

DNS

Tools

-   `wireshark`

    -   filters

-   `tshark`

    -   cli for `wireshark`

-   `tcpdump`   

-   `tcpflow`

    -   like `tcpdump`, but separates traffic into different files

    -   useful for isolating hosts

## Countermeasures

-   encryption

    -   can disincentivize attackers from targeting a system; makes cracking impractical

-   lock down ports

    -   common practice among big organizations

-   MAC address assigning (gateway)

    -   every client using the default gateway has their addresses hard coded

        -   pen tester can no longer MiTM the conversation with the default gateway

-   static entries

    -   network admin is assigning these values

    -   limits pen testers ability to manipulate traffic

-   IPv4 vs IPv6

    -   is the traffic encrypted?

    -   is the network authenticating sources?

    -   are both protocols protected from intrusions?

    -   applies to all communication protocols

-   protocol tunneling

-   Concept of least privilege (COLP)

    -   only give a user access to what they need and NO MORE

-   One time passwords / one time pads (OTP)

-   encryption Protocols

    -   ssh

    -   http(s)

## Tools

#### `BinText`: is a tool for extracting text from binary files. It gives us a high-level overview of what's happening inside of the file. It shows us:

    -   text inside of the file

    -   file position of text

    -   memory position of text

Having this overview of the file gives us clues about what the file is doing. These are things like naming conventions, commands being executed, time stamps, and other useful information to help point us in the right direction.

#### `netstat`: command line tool for displaying protocol statistics and current TCP/IP network connections.

As always, check the `man` page or the help file for ther proper syntax. We're going to focus on a few different flags to help us narrow down our findings to give us exactly what we need.

    - `-a`: displays all connections and listening ports

    - `-e`: displays ethernet statistics. This may be combined with the `-s` option

    - `-n`: displays addresses and port numbers in numberical form

    - `-o`: displays the owning process ID associated with each connection

    - `-p proto`: shows connections for the protocol specified by proto

    - `-r`: displays the routing table

    - `-s`: displays the per-protocol statistics

    - `interval`: redisplays selected statistics, pausing interval seconds between each display.

```bash
netstat -s
```

This will provide us with a summary of the per-protocol statistics IPv4, ICMPv4, TCP, and UDP. This particular flag is useful for getting a quick overview of what is working properly and what is not.

```bash
netstat -a
```

Let's put some tools together like we did in chapter 05 by opening up both `etherape` and `wireshark` in your Kali VM.

#### `macof` (Kali): an ARP flooding tool for switched LAN with random MAC addresses. It floods the network with random MAC addresses, causing some switches to fail open in repeating mode, facilitating sniffing.

We used this tool a few chapters back, and it was explained briefly. Let's try using it again. `man macof` or `macof -h`.

**_ALWAYS SET WITH `-n` FLAG!!! It is possible to denial of service yourself by just firing off the command with no flags!!!! so be careful what you type in when executing_**

An example:

```bash
macof -i eth0 -n 10
```

We've specified `eth0` as the interface with `-i` flag, and the number of times to run with the `-n` flag. In this instance, `10`.

#### `etherape` (Kali): a tool for visually representing traffic on a network.

As in the previous exercise, when we fire off our `macof` command, connections start showing up in `etherape`.

#### `wireshark` (Kali): and packet sniffing tool for minutely auditing network traffic.

Let's open up `wireshark` and start our traffic sniffing. Pick your interface from the menu and then start capture.

Let's ratchet up the `macof` command and have it run 200 times.

```bash
macof -i eth0 -n 200
```

Notice how quickly that `etherape` fills up with activity? We've just flooded the network with 200 bogus addresses in a _very_ short amount of time.

Let's take a look at `wireshark`. All sorts of traffic will continue to generate after the `macof` stops, but we're on the lookout for malformed packets. Let's stop the capture and scroll to the top of the list where we began our attack, we can see exactly that.

Just like before, we used the three tools together:

1.  attack tool, `macof` **ALWAYS SET THE `-n` FLAG!**

2.  visualization tool, `etherape`

3.  sniffer, `wireshark`

    -   `tshark`: command line sniffer tool (use it!)

        -   do the same exercise but using `tshark` from the command line instead of `wireshark`. This can be simpler sometimes, and easier to place the results into documentation. `man tshark` for options. play around with the options and try using both `nmap` and `macof` to create the packets. pay attention to any differences you might notice.

The best way to learn how to analyze network traffic is to generate and watch it. Figure out what's normal and safe, and what's sketchy and weird.

#### `urlsnarf` (Kali): sniffs HTTP requests in Common Log Format.

Always. `man`. your. tools. In your Kali VM:

```bash
urlsnarf >> urlsnarf.pcap
## should get some output here
## we're dumping that information into the .pcap file for later analysis with a program called `wwwstat`.
```

Let's just generate some traffic in our browsers while `urlsnarf` is running. Simply browse around to any old website just to generate some traffic. After a 3 or 4 websites, stop browsing but leave the browser open.

Now, let's check out the `.pcap` file.

```bash
less urlsnarf.pcap
```

#### `wwwstat`(Kali): summarizes WWW server access statistics

This isn't the easiest thing to read, and not great to look at. Let's exit out of the file and install `wwwstat` if we don't have it already.

```bash
sudo apt-get install -y wwwstat
```

I pitty the fool, that don't `man` page that tool. Also, play around with what the tool can do!

We saved the results of our `urlsnarf` to a file with a `.pcap` extension. This allows for the information in the file to be read by `wwwstat`, which we will now use to process the file.

```bash
wwwstat urlsnarf.pcap
```

The information should print to the console. Things look a little bit more organized now, eh? The idea here is to capture the traffic and save it later for analysis. Obviously, this tool has uses on both sides of the isle. If you go through it, the "tidied-up" infos provide a number of interesting nuggets:

-   display format

-   statistics

-   search engine traffic

-   social media Traffic

-   pulling of gravatar images

-   wordpress

    -   plugins

        -   a potential weakness here is the attacker enumerating information about the system and its plugins by naming convention.
