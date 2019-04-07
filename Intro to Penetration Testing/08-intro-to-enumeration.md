# Introduction to Enumeration

##### Theory

In scanning, we were grabbing the information about ports and vulnerabilities, etc. During enumeration, we're still "disclosure-oriented" or "confidentiality-oriented".

So far, we've been gathering more generalized information via scanning and other research. "Enumeration" is the act of putting all of this information together to attempt to gather more specific, targeted information in order to determine our attack.

So we've been running tools against our target. We gather and document this information.

##### some information we could have gathered:

1.  Routing / ARP tables

2.  Users / groups / accounts

3.  network resources

    -   peripherals like printers

    -   networking protocols TCP/IP, etc.

4.  services

    -   provides more potential vectors of attack for gaining access

5.  names

    -   naming conventions?

        -   DNS?

        -   NetBIOS?

        -   no resolution and only TCP/IP?

        -   router with no convention?

6.  applications / banners

    -   possible vectors of attack

7.  passwords

    -   holy grail!

    -   password lists?

        -   google default password lists for different routers and hardware

            -   <https://www.phenoelit.org/dpl/dpl.html> provides a HUGE amount of information

8.  OS's

    -   windows

    -   linux

    -   mac

    -   client/server

##### Protocols for connecting to target

Because there are up to 65k ports to connect to. That's a lot of services. the old "80/20" rule states that only a handful of these are going to gives us the majority of the information that we want. We start with these:

1.  Netbios

    -   older, but still in use

2.  SMTP

3.  LDAP

    -   directory protocols

4.  NTP

    -   timing

5.  SMB

    -   server message bot

6.  DNS

    -   `nslookup`

    -   `dig`

7.  SNMP

    -   community strings

        -   public (PUB)

        -   private (PRI)

        -   management information base browser (MiB)

8.  Ports?

    -   Includes but not limited to:

        -   53 (DNS)

        -   135, 137, 139, 445 (common Windows ports)

        -   161, 162 (SNMP)

    -   3368 (Global Catalog Services)

    -   389 (LDAP)

    -   3389 (remote desktop)

### Review

1.  How is system vulnerable?

2.  What is available to us?

3.  How do we exploit target system?

    -   conventional

        -   hacking tools

    -   unconventional

        -   shoulder surfing

        -   dumpster diving

        -   social engineering

### tools

#### `GetAccnt` (Windows): an older tool that scans for user accounts. Simply by typing in the IP address of our target and the number of accounts we're looking for, we can get some of the following information:

-   Domain/Computer name

-   User ID (500 is always the default administrator account)

-   full names

-   password

-   privileges

-   primary group id

-   accounts

-   number of logons, last logon/logoff

-   account/password expirations and last time password was changed

-   accounts disabled or expired (would want to know before brute forcing)

-   bad password count

-   logon hours (password restrictions? note the conventions)

-   logon server

-   home directories

    -   useful for checking for permissions anomalies in user home directories

#### `nbtStat`(Windows): Displays protocol statistics and current TCP/IP connections using NBT(NetBIOS over TCP/IP)

With `nbtstat`, we're going to run it against ourselves to get some sample output. We'll check our IP address with `ipconfig`, grab the IPv4 address, and plop that into our command.

```bash
nbtstat -A 00.000.00.00 ## If you follow the help file, the `-A` is for providing the IP address. `-a` is for remote machine name. Like always, run the help file with `-h` for proper syntax.

Local Area Connection:
Node IpAddress: [00.000.00.00] Scope Id: []

			NetBIOS Remote Machine Name Table

		Name				Type		Status
	----------------------------------------------
	USERNAM998E<20>		UNIQUE	  	Registered

	## etc, etc.

	MAC Address = 00-00-00-00-00-00 ## your mac here
```

Note the `<20>` section of what came back here. These are binary code identifiers--they can contain significant information, and you'll want to check them against the Microsoft website.

Always consult the help file on how to properly use the tool. Try out all of the different tags and see what results you come up with! Remember, if the help file prints after running a command with a tag, it means that the command returned no relevant information. You'll also want to put this information into your reports (like always).

#### `netDiscover`(Kali): Scanning tool that interrogates layer 2 and layer 3 information from a network.

```bash
netdiscover -i eth0
```

`netdiscover` will now sniff the whole default network. Since nothing is really going on, it will look pretty boring. Let's generate some traffic. In a new terminal, we're going to use `macof` to craft a quick denial of service request.

-   `macof` is an ARP flooding tool

```bash
macof -i eth0 -n 10
```

The `-n 10` is significant. It limits the number of requests(`10`)--without setting this number, it will continue making ~151k floods per minute, so `-n 10` is obviously going to be much better suited to our purposes.

Back in our `netdiscover` terminal, it should now show us a bit more information:

-   IP addresses

-   MAC addresses

-   Count (number of times response came back)

-   Length

-   MAC vendor / Hostname

    -   every vendor is going to be significant information for exploiting and reporting.

    -   peripherals and product information is valuable

We can kill our `netdiscover` session after the devices are found. The information provided by the tool can now be copypasta'd into your report!

#### pstools:

From Microsoft's website:

"The Windows NT and Windows 2000 Resource Kits come with a number of command-line tools that help you administer your Windows NT/2K systems. Over time, I've grown a collection of similar tools, including some not included in the Resource Kits. What sets these tools apart is that they all allow you to manage remote systems as well as the local one. The first tool in the suite was PsList, a tool that lets you view detailed information about processes, and the suite is continually growing. The "Ps" prefix in PsList relates to the fact that the standard UNIX process listing command-line tool is named "ps", so I've adopted this prefix for all the tools in order to tie them together into a suite of tools named PsTools.

Some anti-virus scanners report that one or more of the tools are infected with a "remote admin" virus. None of the PsTools contain viruses, but they have been used _by_ viruses, which is why they trigger virus notifications.

The tools included in the PsTools suite, which are downloadable as a package, are:

PsExec - execute processes remotely
PsFile - shows files opened remotely
PsGetSid - display the SID of a computer or a user
PsInfo - list information about a system
PsPing - measure network performance
PsKill - kill processes by name or process ID
PsList - list detailed information about processes
PsLoggedOn - see who's logged on locally and via resource sharing (full source is included)
PsLogList - dump event log records
PsPasswd - changes account passwords
PsService - view and control services
PsShutdown - shuts down and optionally reboots a computer
PsSuspend - suspends processes
PsUptime - shows you how long a system has been running since its last reboot (PsUptime's functionality has been incorporated into PsInfo"
