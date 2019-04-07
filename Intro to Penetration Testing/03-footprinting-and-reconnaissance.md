# Footprinting and reconnaissance

### `nslookup`

_heads up_:

Depending on OS, your terminal prompt may look different. The model I'm using for commmands is windows/DOS. These commands are very similar between operating systems, and _should_ work similarly for OSX and Linux distros.

In terminal / command prompt:

```bat
C:\Users\$User > nslookup www.website.com
```

this command returns IP and DNS information using _authoritative_ & _non-authoritative_ answers

**_authoritative_**: comes from the name server that has already been specified online

**_non-authoritative_**: comes from somebody else's or your own name server

putting `nslookup` into interactive mode:

```bat
C:\Users\$User > nslookup

> www.website.com
```

your command prompt will have a `>`, allowing you to continue to type in commands for `www.website.com`, remaining in **interactive mode**

**_interactive mode_** is useful for calling up the dns / web address once, and executing commands against it directly. Otherwise, you'd have to tediously enter the dns or address every time you wanted to execute a command against it.

```bat
> help
```

this will show a list of commands

-   `name` / `name 1` / `name 2`

-   `help or ?`

-   `set option`

    -   example: `set type=X`

        -   sets query type and commands

            -   `a` records (host name resolution - matches name to ip ),

            -   `any` (all),

            -   `cname` (alias),

            -   `mx` (mail server),

            -   `ns` (name servers),

            -   `ptr` (host name resolution - matches ip to name),

            -   `soa` (start of authority),

            -   `srv` (service resource records)

-   `server name` specifies server used

-   `ls` can list out all of the domain records

example:

```bat
> set type=a
> www.website.com
```

this will set the parameter `type` to `a` (likely the default anyway). this should return the same result as before, with both the authoritative and non-authoritative answers.

now we will change the record types:

```bat
> set type=MX
> www.website.com
```

the result should return something different, _which is the important part_.

the answer should be `non-authoritative`, because it asked the _local_ name servers as opposed to the _internet_ name servers

this option also shows aliases ( like `canonical name`), mail server ( like `MX preference`), mail service provider ( like `mail exchanger`)

now:

```bat
> set type=SOA
> www.website.com
```

again, will provide the _non-authoritative_ answer, because we are still asking the local name server for the response

the result should show:

-   `primary name server`

-   `responsible mail address`

-   `serial number`

    -   this can often be represented using a date, showing the most recent domain information update

    -   interesting information for hackers, because it can tell you when the domain information was changed

-   `refresh` shows how many times the zone will attempt to be refreshed

-   `retry` works like this:

    -   `refresh` doesn't get a response when attempting to execute until the designated threshold variable.

    after that point,

    -   `retry` will attempt at designated intervals to `refresh` again

-   `expire` is triggered when `retry` and `refresh` don't work for a designated threshold. the name servers will not answer requests after this value is reached.

-   `default TTL` is the default time delay

    -   `TTL` stands for "Time to Live"

we would then document this information.

#### external name server vs. internal name server?

```bat
> server 8.8.8.8 ## google's name server, the result:
Default server: google-public-dns-a.google.com
Address: 8.8.8.8
```

since we `set type=SOA`, we are still in "start of authority" mode. If you type in a different address, some of the same information is returned:

```bat
> www.website.com ## returns some of the same information:
Server: google-public-dns-a.google.com
Address: 8.8.8.8

Non-authoritative answer:
www.website.com
		primary name server = ## same information as before
		responsible mail addr = ## same dns information as before
		serial = ## you get the idea from here
		refresh =
		retry =
		expire =
		default TTL =
```

now, we can change `type` to `a`:

```bat
> set type=a
> www.website.com ## will return different information
Server: google-public-dns-a.com
Address: 8.8.8.8

Non-authoritative answer:
Name: www.website.com
Address: ## IP address goes here
```

now, we set `type` to `MX`

```bat
> set type=MX
> www.website.com
Server: google-public-dns-a.google.com
Address: 8.8.8.8

Non-authoritative answer:
www.website.com		MX preference = 0, mail exchanger = address.of.email.exchanger
```

As a penetration tester, you'd want to record all of this information. Up to this point, we haven't a way of recording the information we've been getting. Let's `exit` from interactive mode by typing--you guessed it--`exit`

```bat
> exit
```

then:

```bat
C:\Users\$User > nslookup www.website.com >> file.txt
Non-authoritative answer:
## this spot stays blank
C:\Users\$User > notepad file.txt
```

the response should be _appended_ to `file.txt`. by using two `>>`.

-   we are _appending_ `file.txt`, so we can now guarantee that the information we're adding to `file.txt` goes at the end of the file, while keeping any information previously stored in the file.

alternately, using one `>` will overwrite `file.txt` with the newly requested information

**protip**: a good method of recording multiple bits of information is to keep _appending_ the requested information to `file.txt`

now, we can change `type` by going back into interactive mode:

```bat
C:\Users\$User > nslookup
>
```

and then we will change the type:

```bat
> set type=MX
> exit
```

now, we'll do the same thing again, printing the newly requested information to the same `file.txt`

```bat
C:\Users\$User > nslookup www.website.com >> file.txt
```

close out of `file.txt` and then open it back up, and we will now see the new information _appended_ to the bottom of the `file.txt` file.

#### `nslookup` review:

have a peek at all of the available `nslookup` commands:

```bat
C:\Users\$User > nslookup
> help
```

Using `nslookup`, we can:

1.  designate server name to apply

2.  set particular options

    -   records

        -   `a` : host name resolution - matches name to ip

        -   `any` : all

        -   `cname` : aliases

        -   `mx` : mail server

        -   `ns` : name servers

        -   `ptr` : host name resolution - matches ip to name

        -   `soa` : start of authority

        -   `srv` : service resource records

3.  `ls`, as always, lists records from designated destinations

### Path Analyzer Pro

I'm running OSX, so I'll be using the mac version of the software.

visit <https://pathanalyzer.com> and find the download for your OS

Mine looks like this:

![visual](http://www.pathanalyzer.com/screenshots/papro_main.png)

Understanding the **_OSI model_** (Open Systems Interconnection model) will be essential to using this application effectively:

<https://en.wikipedia.org/wiki/OSI_model>

check out this video for a high-level overview of how to install path analyzer and how to input information:

<https://www.youtube.com/watch?v=hdnu7ywg4gQ>

### `ping`

`ping` is an activity that tests the connectivity between source and destination

let's see what it can do:

```bat
C:\Users\$User > ping /?
```

This should return the `help` or `man` page for `ping`

options include:

-   `-t` : repeatedly ping the target until process is manually told to stop (`ctrl + c`)

-   `-a` : resolves addresses to host names

-   `-n count` : specify a particular number of responses to return

-   `-l size` : send buffer size

-   `-f` : fragment

-   `-r` : records route (works for IPv4 only)

-   `-w timeout` : timeout value in milliseconds

-   `-4` or `-6` : specifies using IPv4 or IPv6, respectively

example:

```bat
C:\Users\$User > ping www.website.com

## should return something similar to:

Pinging www.website.com [ip.address.here.0] with 32 bytes of data:
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56

Ping statistics for ip.address.here.0:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 41ms, Maximum = 44ms, Average = 42ms

C:\Users\$User >
```

Can also be done "in reverse" by using the `-a` flag:

```bat
C:\Users\$User > ping -a ip.address.here.0

Pinging serverNumber.host.com [ip.address.here.0] with 32 bytes of data:
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56
Reply from ip.address.here.0 bytes=32 time=42ms TTL=56

Ping statistics for ip.address.here.0:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 41ms, Maximum = 44ms, Average = 42ms

C:\Users\$User >
```

this attempts to resolve the same name server and ip address pair in reverse. If it can come up with a name, it will.

Let's try something more advanced using two of those flags mentioned earlier:

```bat
C:\Users\$User > ping www.website.com -f -l 1500

Pinging website.com [ip.address.here.0] with 1500 bytes of data:
Packet needs to be defragmented but DF is set.
Packet needs to be defragmented but DF is set.
Packet needs to be defragmented but DF is set.
Packet needs to be defragmented but DF is set.

Ping statistics for ip.address.here.0:
	Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\$User >
```

what the return means is this: "the packet needs to be defragmented, but the defragmented bit is set"

we can use this to learn a bit about what's going on between our source and our destination

In this case, there's likely to be a firewall, which is usually indicative of not allowing the fragmented bit to be set

let's change the load (`-l`) size to `1200`:

```bat
C:\Users\$User > ping www.website.com -f -l 1200

## should return:
Reply from ip.address.here.0: bytes=1200 time=50ms TTL=56
Reply from ip.address.here.0: bytes=1200 time=49ms TTL=56
Reply from ip.address.here.0: bytes=1200 time=48ms TTL=56
Reply from ip.address.here.0: bytes=1200 time=51ms TTL=56

Ping statistics for ip.address.here.0:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 48ms, Maximum = 51ms, Average = 49ms

C:\Users\$User >
```

Why did changing the load size from `1500` to `1200` change the response? The packet needed to be fragmented at `1500`, but at `1200` it did not.

At this point, you can start adjusting the load ("eyeballing it") to find the exact point where it stops timing out. Increment up by `100` and sort of play "over/under" with load size until you find the "breaking point".

When dialed in, the maximum load you can discover without timing out is valuable information for learning about what's going on between the source and the destination.

What we're attempting to do is gather as much information as possible to figure out exactly how things are configured.

We're eliminating as many variables as possible, so we can best determine the configuration path between the source and the destination.

Let's `ping` with another flag:

```bat
C:\Users\$User > ping -n 2 www.website.com
```

The `-n 2` portion limits the number of `ping` calls to `2`. Let's also say that you get a response similar to this:

```bat
C:\Users\$User > ping -n 2 www.website.com

Pinging www.website.com [ip.address.here.0] with 32 bytes of data:
Reply from ip.address.here.0: bytes=32 time=55ms TTL=56
Request timed out.

Ping statistics for ip.address.here.0:
	Packets: Sent = 2, Received = 1, Lost = 1 (50% loss),
Approximate round trip times in milli-seconds:
	Minimum = 55ms, Maximum = 55ms, Average = 55ms
```

This is a perfect case for `ping`ing again to test if the result was an anomaly. Simply run `ping -n 2 www.website.com` again.

If it returns a `(0% loss)` under `Ping statistics` after a second run, it's a pretty good indicator of an anomaly. Running multiple times with more `ping` request would return a more accurate average.

let's have a gander at `ping /?` again.

you'll notice the `-i` flag, and how it corresponds to `TTL`, or "Time to Live" property. Let's use it:

```bat
C:\Users\$User > ping -i 4 www.website.com

Pinging www.website.com [ip.address.here.0] with 32 bytes of data:
Reply from other.ip.address.here: TTL expired in transit.
Reply from other.ip.address.here: TTL expired in transit.
Reply from other.ip.address.here: TTL expired in transit.
Reply from other.ip.address.here: TTL expired in transit.

Ping statistics for ip.address.here.0:
	Packets: sent = 4, Received = 4, Lost = 0 (0% loss),

C:\Users\$User >
```

so as you can see, the `-i` tag `expired in transit`. `-i` is exclusively for setting `TTL`. Again, TTL stands for "Time to Live", and in this instance helps us find how many "hops" we are away from our target destination.

In our example, the tag `-i 4` could be changed to `-i 6` to help dial in exactly how many "hops" there are between us and our target. Again, our job is to eliminate as many variables as possible.

Let's try exactly that example:

```bat
C:\Users\$User > ping -i 6 www.website.com

Pinging www.website.com [ip.address.here.0] with 32 bytes of data:
Request timed out.
Request timed out.
Request timed out.
Request timed out.

Ping statistics for ip.address.here.0:
	Packets: sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\$User >
```

Ideally, we would cycle through each possibility, adjusting the flag and tweaking it as we go, i.e. `-i 1`, `-i 2`, etc.

This tests for when we either get a response or if we time out, helping to get us closer to eliminating more configuration variables.

Keep testing it on your own, and see what you come up with!

#### `ping` review

1.  `-i` = time to live

2.  `-n` for number of resolution attempts

3.  `-t` to "toggle" persistence until "told to (ctrl + c) stop"

4.  `-a` hostname resolution

5.  `-l size` send buffer size

6.  `-f` fragment

7.  `-4` or `-6` : specifies using IPv4 or IPv6, respectively

get to know all of these utilities. check out all of the options. the only way to get to know this stuff it to try it all out.
