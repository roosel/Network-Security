# Intro to Scanning

### Theory of Scanning:

Source > tools > Target

Some scanning tools:

-   HPING

-   NMAP

Between source and target:

1.  TCP - UDP (layer 4 of OSI)

2.  IPv4 - IPv6/ICMP (layer 3 of OSI)

##### Basics:

-   Hosts (up or down?)

-   Ports/Services

-   Vulnerabilities

-   Creating reconnaissance Diagrams

**_don't have to do this in your head, make charts and graph out the information_**

##### Some tools to master:

1.  ping sweep

-   IPv4 - IPv6 - ICMP

2.  subnet masks

3.  TCP (acronym: SRAF UP)

    -   send, reset, acknowledge, urgent and push

    -   TCP _has_ flags, UDP _does not_

4.  3 way handshake

5.  Banner grabbing

##### Techniques:

_All systems respond to these:_

-   Full Open

    -   completing TCP 3 way handshake

-   Stealth/Half Open

    -   not completing the TCP handshake, just looking for response

_**Not** all systems respond to these advanced techniques:_

-   XMAS (christmas scans) or ACK (acknowledgement scans)

    -   ACK good for detecting firewall or filter detection

-   FIN scans

    -   "starting conversation" with "i'm finished"

-   NULL scans

    -   no tcp flags are set

-   IDLE (IPID) scans or "zombie scans"

    -   get information about target without ever touching it

-   ICMP ECHO scans or "list scans"

    -   incorporate DNS results

-   UDP (no flags)

##### Advanced intrusion detection and evasion while scanning:

-   Fragmentation

    -   helps throw off intrusion detection signatures

-   Tool efficiency

    -   ability to write out command line options so technique does exactly what you want to do and captures the desired results efficiently

-   proxies

    -   hide source from target

-   Tunneling/Encryption

    -   HTTP

    -   ICMP

    -   SSH

-   Anonymizers

    -   tunneling through public website

    -   Tor and other tools

    -   Live boot OS

-   Spoofing source addresses

#### Counter measures:

-   Filtering (ACL)

-   Hiding / Encrypting

-   Anti Spoofing rules

## Review:

1.  Scanning goes from source to Target

2.  Master the "core four" of the OSI model

    -   TCP

    -   UDP

    -   IPv4/6

    -   ICMP

3.  Looking for Hosts

    -   Ports and Services

    -   Vulnerabilities

4.  Advanced Techniques

    -   manipulating commands effectively

    -   covering your tracks
