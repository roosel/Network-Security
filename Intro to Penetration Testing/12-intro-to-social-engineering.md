# Introduction to Social Engineering

Face-to-face or via technology, disclosure of information is the goal. People are the most common attack vector, and often times represent the weakest link the in the security chain.

Elements:

1.  Targets

    -   office workers

2.  concepts / skills

    -   interpersonal skills

    -   talkative

    -   creative

    -   communication

3.  methods

    -   emails

    -   phone

    -   in person

4.  techniques

    -   Computer

        -   spam

        -   chat

        -   chain letters

        -   hoax

        -   pop up

        -   phishing

        -   fake applications

        -   SMS

        -   social sites

    -   Human

        -   legit

        -   VIP

        -   tech support staff

    -   spear phishing (targeting phishing)

    -   mobile devices / malicious applications

    -   eavesdropping

    -   shoulder surfing

    -   dumpster diving

    -   tailgating

    -   in interpersonal

    -   reverse social engineering

        -   target voluntarily gives up information

    -   piggybacking

5.  Impact

    -   Loss of privacy

    -   damages goodwill

    -   out of business

    -   terrorism

    -   financial Loss

    -   theft

    -   identity theft

6.  Vectors

    -   human nature

    -   ignorance

    -   open promises

    -   moral obligation

        -   people want to be helpful

    -   lack of training

    -   easily accessible

    -   weak policies

    -   difficult to detect

        -   benefit of the doubt

7.  value

    -   confidential information

    -   authentication

    -   access control

8.  Countermeasures

    -   change passwords

    -   account lock / logout / expiration

    -   training

    -   keep sensitive information secret

    -   escort vendors

    -   shred documents

    -   strict Least Privilege access control

    -   classification

    -   incident response

    -   background checks / screening

    -   2 factor authentication

    -   change management

    -   antivirus / phishing software

### Intro to DoS attacks

Two types, both focused on availability:

-   Denial of Service

-   Distributed Denial of Service

    -   using multiple computers (bot nets)

1.  concepts

    -   reduces use

    -   restricts use

    -   prevents use

    -   flooding

2.  detection

    -   actively profiling networks

    -   change point analysis

3.  distribution tree

    -   attacker

        -   handler 1

            -   zombies 1

                -   target 1

        -   handler 2

            -   zombies 2

                -   target 1

        -   handler 3

            -   zombies 3

                -   target 1

4.  techniques

    -   Bandwidth

    -   SYN flood (3-way handshake)

    -   ICMP flood

    -   UDP flood

    -   Peer-to-peer

    -   application flood (JAM)

        -   http

        -   php

    -   permanent

        -   phlashing

        -   bricking

        -   sabotage

5.  Countermeasures

    -   absorb it

    -   degrade service

    -   shut down

    -   neutralize botnet

    -   deflect

    -   forensics

    -   updated software / patches

    -   awareness

    -   traffic analysis

    -   detect spoofed addresses

    -   ingress/egress filtering

    -   TCP intercept

    -   load balancing / throttling

    -   system hardening

    -   encryption

    -   dedicated hardware (cisco, forti ddos, arbor, etc.)

### Session Hijacking

Allows the user to do all of the heavy lifting, then step in and take it over.

#### Why?

-   no account lockout

-   insecure session handling

    -   session created before or after authentication? (before is beneficial to the pen tester)

-   small session ID's

    -   only so many possibilities, open to brute forcing

-   weak generation algorithms

-   infinite session time

-   cleartext

-   "predictable" conventions

-   sequence numbers

#### concepts

-   3 way handshake

    -   sync request sent to server

    -   server replies with acknowledgment AND their own sync request

    -   client acknowledgment

-   spoofing vs. hijacking

    -   spoofing: assuming another identity and then connecting to server

    -   hijacking: watching user session and taking over

-   active vs. passive

-   network level vs. application level

-   "session ID"

    -   how created? used? naming conventions?

-   Man in the browser (MITB)

    -   software that uses an internal proxy to sniff traffic from the client and then forward it to the server

        -   happens via browser or via network

    -   "Burp"

-   Man in the middle (MITM)

-   XSS (cross site script)

#### how?

1.  sniff / monitor traffic

2.  desync victim

3.  take over Session

4.  whatever you want

#### where?

1.  url argument

2.  hidden form field

3.  cookies

#### techniques

-   calculate session IDs

-   steal Session

-   brute forcing

-   forged ICMP/ARP

    -   packet crafting software

#### network level

-   blind hijacking

    -   codes goes in, but doesn't show a response

-   udp hijacking

    -   fewer fields than tcp

-   tcp/ip

    -   v4 vs v6

-   reset hijacking

    -   sends reset packet to one side of the handshake

    -   MiTM or fake AP methods

-   ip spoof

#### Countermeasures

-   Encryption (https, ssh, etc.)

-   good random number generators

-   generate session after logon

    -   naturally prevents certain information from being exposed

-   logout functionality

-   2 factor challenge / authentication

    -   more robust security

-   education

-   switches vs. hub/shared medium (AP)

-   strong authentication

-   ARP watch / flood monitoring

-   defense in depth

    -   don't rely on any one technique

-   timeout()

-   keep up to date

-   IPSEC (network encryption)
