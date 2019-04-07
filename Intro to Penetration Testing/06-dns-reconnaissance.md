# DNS reconnaissance

Like before, we'll start our headless Debian and our Kali VM. Once they have both booted and you've logged in, open your browser and enter the ip address of the debian server. As My ip was `192.168.56.111`. You should adjust this to fit your virtual server's static ip. This is Since we've configured our website to serve at `wordpress.debian` in our browser, we'll be running our tools against that.

## `nslookup`

We'll check our Kali interface:

```bash
ifconfig eth0
```

Make note of the `inet` address in the result. Mine was `10.211.55.32`

Now, let's test connectivity from Kali to our Debian server:

```bash
ping 192.168.56.111
```

Next, we'll test our connectivity from our Debian server to our Kali client. From the Debian server:

```bash
ping 10.211.55.32 	# or whatever the inet is for your Kali virtual machine.
```

For good measure, in our mac terminal, we will ping both addresses.

```bash
ping 192.168.56.111
ping 10.211.55.32
```

All three tests should experience 0% packet loss.

In Kali, we'll go into interactive `nslookup` mode.

```bash
nslookup
>	# this character should appear after pressing return
```

We're going to go into `nslookup` on our Debian machine as well.

On the debian side, enter

```bash
> server 	# which will return something similar to:
Default server: 10.112.172.1
Address: 10.112.172.1#53
```

Back on the Kali side, we're going to set our `nslookup` target to wordpress on our debian server.

```bash
> server	# the following should return similarly to this:
Default server: 10.211.55.1
Address: 10.211.55.1#53
```

You can then type in

```bash
> wordpress.debian	# which should return similarly to:

Server:		10.211.55.1
Address:	10.211.55.1#53

Name:	wordpress.debian
Address:  192.168.56.111
```

This shows us that `wordpress.debian` is originating from the IP address `192.168.56.111`, which matches up exactly with the `inet` address on our Debian machine.

Like all of the other tools, `nslookup` has a number of commands, and a few have changed over the years. As always, it's a good idea to check the man pages to stay up to date and learn how to properly use this command for your applications. For example, the `ls` command was deprecated in favor of `host`, which is the command we will be using.

Let's start by switching types.

```bash
> set type=mx  # then, we will:
> host wordpress.debian 	# which for me returned:

Server:		10.211.55.1
Address: 	10.211.55.1#53

Non-authoritative answer:
**** Cant find host: No Answer
```

Which was then followed by a list under the header `Authoritative answers can be found from:`

Let's try switching `type` to `SOA`:

```bash
> set type=SOA
> host wordpress.debian  # which would return something like:

Server:		10.211.55.1
Address: 	10.211.55.1#53

Non-authoritative answer:
host
		origin = ns0.centralnic.net
		mail addr = hostmaster.centralnic.net
		serial = 0000000000000000000   # your serial number should be here
		refresh = 900
		retry = 1800
		expire = 6048000Â
		minimum = 3600

Authoritative answers can be found from:
host	nameserver = a.nic.host.
host	nameserver = c.nic.host.
host 	nameserver = d.nic.host.
host	nameserver = b.nic.host.
a.nic.host		internet address = # IP address goes here, and then a bunch of other information will be below that looks similar
```

**_note: don't forget to take your notes and make your documentation while you go through your audits. copypasta to a new text file or use the command line tools to create and append a new text file using `>>`_**

For the most part, we shouldn't really come up with much more information, regardless of the `type` setting. In the future, we'll work with a more exploitable OS in order to demonstrate these tools in action.

One of the cool things about this tool is that if zone transfers have been allowed on the server, you can print all of the information you'd normally gather from multiple scanning tools simply by firing off a few commands. Neat, huh?
