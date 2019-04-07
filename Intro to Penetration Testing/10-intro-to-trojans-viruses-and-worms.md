# Introduction to Trojans, viruses and worms

Until now, we've been focused on disclosure. Now, we will focus on integrity.

## Trojans

What this particular attack depends on is getting the targeted user to execute a malicious application. This means we want to craft the program in such a way that it gets the target to click on it. Trojan applications could be a considered a combination of both art and science.

-   two components ("The wrapper")

    1.  overt

        -   what the user sees

            -   freeware

            -   software

            -   games

    2.  covert

        -   what is the hacker trying to do?

        -   backdoors

        -   root kits

        -   keyloggers

-   Types

    -   VNC

    -   HTTP

    -   IRC  

    -   Command shell

    -   destruction

    -   documents

    -   botnet

    -   proxy

    -   RAT (remote administraion tool)

    -   email

    -   FTP

    -   GUI

    -   Spam

    -   Defacement

    -   Mobile

-   Infection techniques

    -   IMs

    -   IRC

    -   Physically

    -   browser

    -   fake programs

    -   attachments

    -   advertisements for freeware or bundled utility

    -   game

-   Evasion

-   Indicators

    -   kit types

    -   construction kits

    -   common ports

    -   port selection patterns for attack

-   Detection

    -   ports

    -   running processes

    -   registry

    -   drivers

    -   services

    -   startup

    -   files / folders

    -   network activity

    -   scanners / antivirus software

-   Countermeasures

    -   email attachments

    -   IMs

    -   STIGs (tech standards)

    -   Monitor traffic

    -   system hardening

        -   deny access to certain install and execute tasks

    -   block ports

    -   patch managements

    -   monitor integrity

    -   trusted sources

    -   anti "trojan" software

-   Tools

This is a short list of various tools available. Test these out! Usually the name of the program followed by `-h` or `help` will give you usage information. Sometimes simply typing in the name with no arguments will provide the same. Finally, if neither of those options is available, there's always the `man` page.

-   `fport` (Windows):

    -   maps PID, name of process, ports used, protocol, and system path.

    -   when investigating trojans, you want to know if it's actually being executed (PID), if it's connecting to other systems (ports), and where it's being executed from (path).

    -   when penetration testing, you're interested in the same three components, but for different reasons.

    -   think forensics is trying to find, hacker is trying to hide.

        -   important to manipulate these three values so that they don't appear suspicious. Change:

            -   choose a location and name to something matching system conventions.

            -   change default ports

            -   change name of tool being run

                -   renaming system processes

        -   manipulation of these values makes forensics more difficult.

-   `ICMPsrv` (Windows)

    -   ICMP tunneling tool

    -   client (`icmpsend.exe`) option and server option (`icmpsrv.exe`)

-   `md5sum` (various Linux, in our case, Kali)

    -   tool for printing the md5 hash of a file

    -   useful for checking the integrity of a file

        -   if a file has steganographic content (hidden, possibly malicious), the sums will be different.

## Viruses & Worms

The concept of a worm is similar to a virus, in that the goal is the same.

-   Alter data

-   corrupt data

-   propagate

-   hide

-   mutate

-   affect system integrity

### The difference:

**Worms:** _self replicating malicious software (tends to attack windows services)_

**Viruses:** _attack the logged in user_

#### Stages of the Virus:

1.  design

2.  replication

3.  launching mechanism

    -   fragmentation techniques?

4.  Detection

    -   scanning software

    -   hashing

5.  Eradication

#### Types of viruses and worms:

-   boot sector

-   file / multi partite

-   macro

-   tunneling

-   sparse

-   polymorphic (many changes or iterations)

-   metamorphic (changes characters)

-   shell

-   cavity

-   terminate & stay resident

#### Distribution

-   Search engine optimization

-   clickjacking

-   phishing

-   malware

-   hijacked legitimate websites

-   drive by "free wifi"

#### Malware analysis

-   isolated testing environments

-   network sensors and antivirus software

-   virtual machines

#### Countermeasures

-   antivirus software

    1.  integrity-based

    2.  knowledge / signature / rule-based

    3.  anomaly-based systems

-   organizational policies

-   patch management

-   system hardening

-   user training

-   authenticating senders

-   scanning

-   firewalls / ACL's (access control lists)

-   popup / adware blockers
