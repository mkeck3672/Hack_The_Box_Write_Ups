<H1 style="text-align: center;">Hack The Box </H1>
<H2 style="text-align: center;">Devel write up </H2>
<H2 style="text-align: center;">By Michael ThugBounty Keck </H2>
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel.png"></p>

Recon:
Started the test out by running Rustscan to get the open ports. 
Command: rustscan -a 10.10.10.5

|Status|IP|Port|
| -----|--|----------- |
| Open | 10.10.10.5 | 21 |
| Open | 10.10.10.5 | 80 |

PORT 21: 
Checked to see if FTP would allow for anonymous login
Command: ftp 10.10.10.5
Found that anonymous login was allowed 
I used the following creds anonymous:anonymous

PORT 80:
In FireFox I navigated to the page using the ip
I found the standard IIS7 page. 

USER SHELL:
