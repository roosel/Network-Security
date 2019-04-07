# Introduction to hacking web servers

1.  Products

    -   IIS (17%) (Microsoft)

    -   Apache (65%)

    -   Nginx (13%)

    -   Google

    -   Lighttpd

2.  Impact

    -   website defacement

    -   compromise(s)

    -   data tampering

    -   " " theft

    -   pivot points

        -   potential access to the internal network

3.  Techniques

    -   Directory traversal (`../`)

    -   http response splitting

        -   attacker sends multiple requests getting mutiple responses

    -   web cache poison

        -   insert malicious code into the cache

    -   ssh brute force

    -   MiTM

    -   password cracking (Dictionary, Brute force, Hybrid)

    -   form tampering

    -   command injection

    -   cookie tampering

    -   buffer overflow

    -   DoS/DDoS

    -   Cross site request forgery

    -   SQL injection

    -   Cross site scripting

    -   session hijacking

4.  Vectors

    -   unnecessary files and backups left behind

    -   security conflict vs. functionality

    -   default settings

    -   permissions (RWX)

    -   misconfigurations

    -   default accounts (admin/password, guest)

    -   security flaws / bugs

    -   temporary SSL certificates

    -   improper authentication

    -   no system hardening

    -   Joomla, Drupal, Wordpress

    -   Verbose errors

    -   anonymous users

    -   sample configs / scripts

    -   remote administration

    -   unnecessary services

5.  Methodology

    -   information gathering

    -   footprint / surface area of attack

    -   mirroring

    -   vulnerability scans

    -   exploitation

6.  Countermeasures

    -   patch process at intervals

    -   alternative sites and servers (stand-by or offline)

    -   test in non-production environment

    -   backups

    -   hire good security people

    -   protocol analysis

    -   monitor accounts / purge deactivated account

    -   monitor files and directories

    -   encryption

    -   tested, known good architecture

        -   separate presentation, application, and data into 3 zones

    -   vulnerability scan (`nikto`, `nessus`, etc.)

    -   Data Loss Prevention

        -   clear definitions and categorization of security-sensitive information

            -   information quarantined for evaluation / exemption

        -   user-friendly patch management process
