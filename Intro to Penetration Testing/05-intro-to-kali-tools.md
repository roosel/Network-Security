# Introduction to Kali Linux tools

In order to continue with beyond this point, you will need to follow the previous guide on installing a headless debian server first.

You'll need to have Kali _and_ the debian server running at the same time. Start the server in headless mode using Virtualbox, let it boot then ssh into the server from terminal. Don't forget to pull up the server's ip address in your browser to make sure that the server is... serving. Wherever I used the ip address `192.168.56.111`, replace it with the static ip address of your headless debian server. Also, wherever I've used `vim` replace with whatever text editor you prefer. Or be badass and start using `vim`.

It should be noted that I'm using two different pieces of software for my guest operating systems. Kali in Parallels, and Debian in virtualbox headless.

### DNS Enumeration

Kali Linux has several tools for DNS analysis. Some of these include `dnsmap`, `dnsrecon`, `dnswalk`, `nmap`, `zenmap` (`nmap` frontend).

Let's focus on `dnsemum`.

`dnsenum` is a tool that provides us with a report including certain information. These commands are possible to run separately, but why do all the heavy lifting when there's a script already out there for that? `dnsenum` is used in Kali command line like so:

```bash
dnsenum www.website.com
```

This script will run and show us a quick rundown of the following:

-   name of the website

-   the public host IP addresses

-   the public name servers

-   the mail servers (if found)

-   zone transfers (if found)

For further help with the advanced options, simply type into the command line `dnsenum`, and it will show all of the options available inside of the tool.

As with all command line tools, we will run the help file, run our command against the target, get the output, document output information, then move on to the next task.

### Advanced pinging/packet crafting tools

Let's sniff traffic and set specific flags using different tools and verify:

Let's open `wireshark` and `etherape`. The first comes with most distributions of Kali, but the latter might not.

In order to install `etherape`, you'll need to add the following to your `/etc/apt/sources.list` file:

```bash
deb http://http.kali.org/kali kali-rolling main contrib non-free
```

Then you will `apt-get update` and `apt-get install etherape`. If you haven't `apt-get dist-upgrade`d by now, this is a good point to do so, because now we have all of the tools that we currently need. Also, let's `apt upgrade` as well.

Moving on,

We will then pull up the interfaces in the `wireshark` gui. It should be a little gear near the upper left of the window. Select the appropriate interface. The one available at the top is usually the default, but we'll go ahead and select that one anyway. In my case, it was `eth0`.

We will also make sure to have `etherape` open. I recommend making your terminal slightly translucent, and keep `etherape` running behind it.

Now we will return to the terminal, and start learning about `hPing3`:

```bash
hping3 -h
```

This should show your `help` menu, with all of the available options and flags for the program:

```bash
usage: hping3 host [options]
  -h  --help      # show this help
  -v  --version   # show version
  -c  --count     # packet count
  -i  --interval  # wait (uX for X microseconds, for example -i u1000)
      --fast      # alias for -i u10000 (10 packets for second)
      --faster    # alias for -i u1000 (100 packets for second)
      --flood	  # sent packets as fast as possible. Dont show replies.
  -n  --numeric   # numeric output
  -q  --quiet     # quiet
  -I  --interface # interface name (otherwise default routing interface)
  -V  --verbose   # verbose mode
  -D  --debug     # debugging info
  -z  --bind      # bind ctrl+z to ttl           (default to dst port)
  -Z  --unbind    # unbind ctrl+z
      --beep      # beep for every matching packet received
Mode
  default mode     # TCP
  -0  --rawip      # RAW IP mode
  -1  --icmp       # ICMP mode
  -2  --udp        # UDP mode
  -8  --scan       # SCAN mode.
                   # Example: hping --scan 1-30,70-90 -S www.target.host
  -9  --listen     # listen mode
IP
  -a  --spoof      # spoof source address
  --rand-dest      # random destionation address mode. see the man.
  --rand-source    # random source address mode. see the man.
  -t  --ttl        # ttl (default 64)
  -N  --id         # id (default random)
  -W  --winid      # use win* id byte ordering
  -r  --rel        # relativize id field          (to estimate host traffic)
  -f  --frag       # split packets in more frag.  (may pass weak acl)
  -x  --morefrag   # set more fragments flag
  -y  --dontfrag   # set dont fragment flag
  -g  --fragoff    # set the fragment offset
  -m  --mtu        # set virtual mtu, implies --frag if packet size > mtu
  -o  --tos        # type of service (default 0x00), try --tos help
  -G  --rroute     # includes RECORD_ROUTE option and display the route buffer
  --lsrr           # loose source routing and record route
  --ssrr           # strict source routing and record route
  -H  --ipproto    # set the IP protocol field, only in RAW IP mode
ICMP
  -C  --icmptype   # icmp type (default echo request)
  -K  --icmpcode   # icmp code (default 0)
      --force-icmp # send all icmp types (default send only supported types)
      --icmp-gw    # set gateway address for ICMP redirect (default 0.0.0.0)
      --icmp-ts    # Alias for --icmp --icmptype 13 (ICMP timestamp)
      --icmp-addr  # Alias for --icmp --icmptype 17 (ICMP address subnet mask)
      --icmp-help  # display help for others icmp options
UDP/TCP
  -s  --baseport   # base source port             (default random)
  -p  --destport   # [+][+]<port> destination port(default 0) ctrl+z inc/dec
  -k  --keep       # keep still source port
  -w  --win        # winsize (default 64)
  -O  --tcpoff     # set fake tcp data offset     (instead of tcphdrlen / 4)
  -Q  --seqnum     # shows only tcp sequence number
  -b  --badcksum   # (try to) send packets with a bad IP checksum
                   # many systems will fix the IP checksum sending the packet
                   # so youll get bad UDP/TCP checksum instead.
  -M  --setseq     # set TCP sequence number
  -L  --setack     # set TCP ack
  -F  --fin        # set FIN flag
  -S  --syn        # set SYN flag
  -R  --rst        # set RST flag
  -P  --push       # set PUSH flag
  -A  --ack        # set ACK flag
  -U  --urg        # set URG flag
  -X  --xmas       # set X unused flag (0x40)
  -Y  --ymas       # set Y unused flag (0x80)
  --tcpexitcode    # use last tcp->th_flags as exit code
  --tcp-mss        # enable the TCP MSS option with the given value
  --tcp-timestamp  # enable the TCP timestamp option to guess the HZ/uptime
Common
  -d  --data       # data size                    (default is 0)
  -E  --file       # data from file
  -e  --sign       # add "signature"
  -j  --dump       # dump packets in hex
  -J  --print      # dump printable characters
  -B  --safe       # enable "safe" protocol
  -u  --end        # tell you when --file reached EOF and prevent rewind
  -T  --traceroute # traceroute mode              (implies --bind and --ttl 1)
  --tr-stop        # Exit when receive the first not ICMP in traceroute mode
  --tr-keep-ttl    # Keep the source TTL fixed, useful to monitor just one hop
  --tr-no-rtt	    # Dont calculate/show RTT information in traceroute mode
ARS packet description # (new, unstable)
  --apd-send       # Send the packet described with APD (see docs/APD.txt)
```

This is an overview of how the program operates, and it's a good idea to play around with all of the tools at your disposal in order to master them.

Moving on,

`hping3` is a powerful packet crafting and scanning tool.

You'll notice at the top of the `help` page, is the `[options]`.

Among these flag options are

-   `-h` for help

-   `-v` for version

-   `-n` for numeric output

-   `-q` quiets terminal output

-   `-i` specifies intervals

-   `-I` for interface name (otherwise default routing interface)

-   `-V` for verbose mode

-   `--beep` to beep for every matching packet received (seems like a silly option at this point, because the beeps get a little annoying).

The next section is `mode`, which includes 5 modes:

1.  `-0` for raw IP mode

2.  `-1` icmp mode

3.  `-2` udp mode

4.  `-8` scan mode

5.  `-9` listen mode

Below that, we have the `IP` section, and below _that_ are the `ICMP`, `UDP/TCP`, `Common` and `ARS packet description` sections. As you can see, the `help` page briefly explains what each of the flags does.

`ICMP` has _types_ and _codes_. The most popular are types `8` (echo request), `0` (echo reply), `11`, and `13`.

Now, we will use `hping3` with some additional flags. We'll be using the local `192.168.56.111` (a debian server that I have setup in another virtual machine using virtualbox) IP address to illustrate how this tool works.

```bash
hping3 -SU 192.168.56.111
```

We given the `-S` SYN tag, and the `-U`URG flag. Hit enter and let the command run for a few seconds. After it's given you a few lines of output, `ctrl + c` out of the process, and note the packet loss. It should return something similar to this:

```bash
--- 192.168.56.111 hping statistic ---
7 packets transmitted, 7 packets received, 0% packet loss
round-trip min/avg/max = 3.5/5.1/7.2 ms
```

Mine says 7 because I let it run 7 times before stopping the process with `ctrl + c`

If you still have `wireshark` and `etherape` running (which you _should_), let's take a look at what happens while we use `hping3` against our target IP.

#### `etherape` from wikipedia:

**_"Network traffic is displayed using a graphical interface. Each node represents a specific host. Links represent connections to hosts. Nodes and links are color-coded to represent different protocols forming the various types of traffic on the network. Individual nodes and their connecting links grow and shrink in size with increases and decreases in network traffic."_**

-   basically, think of `etherape` as a graphing tool. It visually represents all of the connections and hosts (think reconnaissance charting).

Moving on,

In `wireshark`, we are going to stop the capture. Next, in the "Filter" box, type in `ip.dst == 192.168.56.111`. To get to the most recent response, scroll all the way down to the bottom. Here is where we are going to start piecing out the packet for information.

Take a look at the TCP (`Transmission Control Protocol`) selection, and you'll see a drop down list of packet information. Let's take a look at the TCP flags. You'll notice that next to `Flags: 0x022` is `(SYN, URG)`. This shows us that the two flags we used during our `hping3` session (`-SU`)--the `SYN` and `URG` flags--were active. This tells us that the program is doing exactly what we've told it to do.

That's a lot to take in. Maybe we should back up a bit. Let's look more closely at the overview of information about the packet provided to us by `wireshark`:

**Layer 2 information:**

-   `Frame 97: 54 bytes on wire (432 bits)...`

-   `Ethernet II, Src: ...`

**Layer 3 information:**

-   `Internet Protocol Version (4 or 6), Src: ...`

**Layer 4 information:**

-   `Transmission Control Protocol, Src Port: ...`

#### Back to how we are using these tools together:

-   `hping3` is crafting the packets

-   `etherape` is visualizing our packet transmission

-   `wireshark` is our packet sniffer

Let's drop down the `IPV` section. We want to match what we saw in the `hping3` help file to the information that is in the packet we sent.

We'll have:

-   header `Version: 4`

-   `Header length: 20 bytes` (always true)

-   Differential Services field

-   Total length

-   flags

    -   in this case, IP flags. (fragment or don't fragment bit)

-   protocol layer ("serving to") (i.e. `Protocol: TCP (6)`, in this case meaning that the current protocol is `IPv4`, "serving to" `IPv6`)

-   source / destination addresses

    -   IP

    -   GeoIP

Let's open up the TCP (`Transmission Control Protocol`) section again. It shows us:

-   source / destination ports

-   relative sequence number

-   acknowledgments

-   Flags

    -   take note of the value next to `Flags:`--in this case `0x022`--as it carries some significance. As an example, `wireshark` filters can be written with this value. This will be explored more later.

-   window size

-   checksum

    -   manipulation of the checksum can defeat some intrusion detection systems

        -   turning these checksums off

        -   setting these values to `true`

    -   some systems will determine these values as valid and leave them alone or unchanged.

#### Review

1.  `hping3` crafts our packets to transmit to our target

2.  `etherape` visualizes our transmission

3.  `wireshark` verifies that the packet we crafted transmitted as expected

    -   think of packet sniffing as a third-party evaluation process

    -   analysis is available per packet, allowing us to minutely audit our packet transmission

    -   "equivalents" to `wireshark`

        -   `tshark`

        -   `tcpdump`

        -   others...

### `nikTo`

Let's say for argument's sake that you've found a vulnerable machine on the network, and want to do a little more reconnaissance.

Let's check the host IP address:

```bash
ifconfig eth0
eth0	Link encap:Ethernet  HWaddress 00:00:00:00:00:00 # your MAC address in place of 00:00:00:00:00:00
# ...
```

It should return the desired information. In this case, it shows us that we are connected to the `192.168` network.

Then, we'll ping the IP:

```bash
ping 192.168.56.111
```

This will verify if we are connected and have activity. If you still have `etherape` open, it will show you a visualization of the connection.

Now, let's use `nikto`, a vulnerability testing tool:

```bash
nikto
```

This will return the help file, and show us the flags and options available to the tool. Among these options are:

-   set a configuration file

-   turn display of outputs on/off

-   select target hosts

-   select output settings (i.e. print to console by default or print to a text file)

    -   particularly useful for documentation

-   disable/force ssl

-   disable 404 checks

-   set ports

-   privileges

-   updates

Let's start exploring the tool.

```bash
nikto -h 192.168.56.111
```

We'll let that run until it's finished. In the future, we'll be using an application called `screen` to detach ourselves from lengthy command line operations like this.

You'll notice that back in `etherape`, the traffic visualization starts going red. This is expected, and illustrates just how much traffic is being generated between us and our target.

Back in terminal, we will see a summary of our operation. What it returns will vary from server to server, configuration to configuration. At this time, it would be wise to search online for what those particular returns mean. In some cases, `nikto` will provide a link to an article about a given vulnerability. Play around with it, but **only** test it on a server that you own or have permission to audit. It might draw the wrong kind of attention if you start firing off commands while poking around the internet with hacking tools.

Now, if we change the command slightly, we can append the information to a text file of our choosing. The `>>` means "redirect and append". Technically, we could also use a single `>` which also _overwrites_ the contents of an existing text file. We can create the file with either command. We use `>>` by convention, because when documenting the results of a security audit, it's best practice to _append_ the documentation rather than create a new text file for each test. (there is obviously some utility in using `>`(_overwrite_) vs. `>>`(_append_), it just isn't relevant to what we are currently doing)

```bash
nikto -h 192.168.56.111 >> niktoScan-mm-dd-yyyy-cccc.txt
```

Let that run, and the output will be _appended_ to our new text file, `niktoScan-mm-dd-yyyy-cccc.txt`. That format is `month-date-year-caseNumber.txt`

After appending our document, we can then open it with any preferred text editor. This will verify for us that the document was created as intended and that it contains the information we intend it to.

```bash
vim niktoScan-mm-dd-yyyy-cccc.txt
```

### `nMap`

As we _always_ do, let's start by opening our help file:

```bash
nmap -h
```

We're going to focus on this part:

```bash
Usage: nmap [Scan Type(s)] [Options] {target specification}
```

Let's start with `{target specification}`. This allows you to attach flags in order to pass:

-   hostnames

-   ip addresses

-   networks

-   etc.

**some flags of note**:

-   `-iL` flag allows you to input ip addresses from a list instead of entering them manually. Pretty handy, huh?

-   `-iR` allows you to choose targets randomly.

    -   Helpful for avoiding detection.

    -   we are using `etherape` to illustrate how "noisy" a regular scan can be.

        -   if you don't send any commands, you'll get broadcast traffic (nothing of interest)

Let's use the `nmap` tool to scan our server, and we'll also create and _append_ a new text file to hold our results:

```bash
nmap -sT 192.168.56.111/24 >> ./Desktop/nmap\ test\ Debian
```

-   Alternately, we could have used `192.168.56.0` to scan for every IP that has `192.168.56` in common. The `0` in this instance means _all_. `nmap` can further subnet these addresses, but we won't worry about that for now.

What that does is try to complete a 3-way handshake with every address that has the desired IP address in common.

`etherape` should be showing you a mighty cool looking graph at this point. Have a gander.

_while we wait_... let's talk about those differences tho:

`-sT` means "scan target"

Why we've appended the ip with `/24`:

-   from <https://nmap.org/book/man-target-specification.html>:

"You can append your target with `/<numbits>` to an IP address or hostname and Nmap will scan every IP address for which the first <numbits> are the same as defined for the reference IP or hostname given. For example, 192.168.10.0/24 would scan the 256 hosts between 192.168.10.0 and 192.168.10.255, inclusive. 192.168.10.40/24 would scan exactly the same targets."

As before, `etherape` is showing us the traffic between us and the target(s). Once the scan is complete, your results will print to terminal. This shows us some very useful information about the network we're connected to.

This took some time for me. Coffee break.

Once the scan is complete, we'll open up the text file that we created (and _appended_ (`>>`)) and look at what our scan returned. We'd be looking for ports that are marked `open` underneath the `STATE` column. Have a look around and see what you find out. If you don't know already, google (and DDG **wink**) search what each of those ports and protocols means.

If we pop back over to `wireshark`, you can see all of the traffic. Once we're done scanning, we would then filter the traffic to get our desired information.

In the "Filter" bar above the results, type in `ip.dst==192.168.56.82`. This limits our results to traffic coming _from_ the destination

Here are some useful filters for `wireshark`, and what they mean:

-   `.src` = traffic from source ip (us)

-   `.dst` (what we just used) = traffic from destination ip (target)

-   `.addr` = all traffic between source and destination. (both to _and_ from)

Let's try something similar, but this time we'll do a _stealth scan_, where we essentially spoof our IP address while scanning.

Instead of `nmap -sT`, we'll use `nmap -sS`. Since we just took forever on that last file, we'll just let this run for a minute or so and then `ctrl + c` to stop.

```bash
nmap -sS 192.168.56.111/24 >> ./Desktop/nmap\ stealth\ Debian
```

We're going to have a look at the file we just created for basically the same information, this new flag just altered how we were _sending_ packets this time.

```bash
vim ./Desktop/nmap\ stealth\ Debian
```

Just replace `vim` with your desired text editor.

At this point, it's not really important to print these results out into text files. I just wanted to illustrate a "best practice". This is practice, so you can just as well view your results from the terminal and copypasta to a text file. From this point on, I'm going to leave out the `>>`.

Let's find out a little bit more about one of the open ports we found. My scan returned an `open` `ftp` at port `21` on address `192.168.56.1`, so let's test what operating system it's running by adding the `-O` flag:

```bash
nmap -O 192.168.56.1
```

This should return _much_ faster since we're only targeting one specific address. It's looks like this one is protected by a firewall.

```bash
Warning: OSScan results may be unreliable because we could not find at least 1 open port and 1 closed port
Device Type: firewall
## etc, etc.
```

So our scan didn't return a definitive answer regarding the target OS. We could go in a little bit further and then append the ip address with `/24` to scan the entire network like this:

```bash
nmap -O 192.168.56.111/24
```

With this syntax, you'd be scanning _all_ of the same servers again, and this will take a lot of time. This issue can be solved with good organization.

Remember earlier on in the lesson when we mentioned the `-iL` flag? Here's a strategy for filtering your results to only the relevant information:

1.  Go through your initial scan's text file, and look for ports that are marked `open`. Take note of these ip addresses.

2.  Copypasta those ip addresses to a new text file, named `openPorts.text`, listing only **_one_** address per line.

3.  In terminal, scan the OS of the ip addresses from the list:

```bash
nmap -iL /path/to/openPorts.txt -O
```

This will only scan for the OS of the addresses from that list.

Let's do another one! This time, we'll use the `-X` (XMAS) flag tacked onto the `-s` flag:

```bash
nmap -sX 192.168.56.1
```

Again, I didn't find any definitive results here. I returned:

```bash
All 1000 scanned ports on 192.168.56.1 are closed
```

So how come when we did a tcp (`-sT` and `-sS`) scan, we got results, this time (`-sX`) we did not?

As we learned from our earlier scan, it's probably because of the firewall. This time we set the `FIN`, `URG`, `PSH` flags.

The answer is that without previously making a connection to the server via the TCP 3-way handshake, this port will remain closed off to us. This will be consistent across the Xmas (`-X`) scan, the fin (`-F`) scan or the null (`-N`) scan.

Let's try scanning ourselves. Enter into terminal:

```bash
ifconfig
```

Grab your `inet` address from the result. We're going to go through all of the scans we've already tried and test them against ourselves. You _shouldn't_ find an opening from any of these results. Replace `-flag` with any of the flags we previously used. In fact, try them all out.

```bash
nmap -flag 10.211.55.32
```

The system we've set up to run against so far has been robust enough to resist any of these scans.

Let's try out a ping scan:

```bash
nmap -P 192.168.56.111/24
```

This should return `Illegal Argument` and tell you to use a variant of the `-P` flag. The `-P` flag cannot be used by itself in this context. Let's get a good look at dat help file tho:

```bash
nmap -h
```

Find the specific ping scan syntax.

-   As always, since this is Unix, cApItAlSmAtTeR! `-P` and `-p` are completely different flags and should not be confused with one another.

Scroll up through the help file to find the `-P` options. You should notice two rows, one with `PS/PA/PU/PY[portlist]` and another with `PE/PP/PM`. We're going to use `-PE` for Icmp echo.

My results showed me that both the `ssh` and `http` ports are open at `22` and `82` respectively. These are defaults and present a potential weakness. ssh brute force attacks are common.

Let's switch over to `UDP` using `-U`:

```bash
nmap -sU 192.168.56.111
```

**A note about what we saw in etherape**:

When we were scanning a whole mess of servers, the graph in `etherape` looked more like a circle, with many connections making the graph. When we're targeting specific addresses, the visualizer shows more direct or straight connections.

### `netDiscover`

Let's open up our tool:

```bash
netdiscover -h
```

This will open up our help file and show us a little bit more about the network.

Now let's scan traffic on our local interface, in this case `eth0`

```bash
netdiscover -i eth0
```

As you can see, there isn't any interesting traffic being shown here. Let's craft some ARP packets using `macof`(an ARP flooding tool) to to take a look at this tool in action. We're going to append our command with `-n 10` so that the ARP flooding tool doesn't keep hitting our `eth0` at 151k floods per minute, and only runs 10 times.

```bash
macof -i eth0 -n 10
```

The traffic from `macof` should be showing now in `netdiscover`.

Think of this tool is a really quick way to take a look at layer 2 and 3 addresses on a network.

### `smbclient`

Let's try out this next tool out:

```bash
smbclient	# this will pull up the help file and tell us more about what the tool can do.
```

Now, let's do something with it.

```bash
smbclient -I 192.168.56.111 -L wordpress.debian -N -U
```

This should return an error:

`Connection to 192.168.56.111 failed (Error NT_STATUS_CONNECTION_REFUSED)`. This is because we used the `-N` and `-U` flags, indicating that we were not specifying a user (which would have come after the `-N` flag) or a password (would have come after the `-U`). You can tell by the word `REFUSED` in the error that this is because of a security setting on our server. This means our Debian server passed the test. Nice!

## Review

1.  We use our tools together to get the best representation of what we are doing when using a tool. Visualization with `etherape` is my preferred option, but you're welcome to find your own.

2.  We read the help files and man pages (and google) to find the appropriate options to use with our tools. Dig for answers.

3.  We print the results of any of our tests to a txt file for documentation.

4.  These tools help us to eliminate configuration variables between the source and target.
