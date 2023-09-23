<h1>Analyze Network Traffic with tcpdump</h1>

<h2>Software Installation</h2>
- tcpdump
- Wireshark
- Visual Studio Code
<br/><br/>

<h3>Install tcpdump</h3>

To install tcpdump on an Ubuntu system, I’ll use the ‘apt’ package manager. First, it’s good practice to update the package list to get latest version of software. Open a terminal and run: 
<br/>
*'sudo apt update'*
<br/>
Once package list is updated, install tcpdump with:
<br/>
*'sudo apt install tcpdump'*
<br/>
After installation completes, I can check version to ensure it was installed correctly:
<br/>
*'tcpdump –version'*

<img src="https://i.imgur.com/QRwFZKs.png" alt="image"/>
<br/><br/>

<h3>Install Wireshark</h3>

Next I will install Wireshark with:
<br/>
*'sudo apt install wireshark'*

<img src="https://i.imgur.com/R2C7rlU.png" alt="image"/>
<br/>

Then I need to configure Wireshark for non-root users. Capturing packets with Wireshark requires superuser (root) permissions. However, for security reasons, its generally not recommended o run graphical applications as root. Instead, I can configure Wireshark to allow members of the ‘wireshark’ group to capture packets. Then add a ‘wireshark’ group, however this step was optional as the installation process already created the group. 
<br/>
*'sudo addgroup --system wireshark'*

<img src="https://i.imgur.com/QSQS4Yq.png" alt="image"/>
<br/>

Change the group ownership of the dumpcap utility (which Wireshark uses for packet capture:
<br/>
*'sudo chgrp wireshark /usr/bin/dumpcap'*

<img src="https://i.imgur.com/WibSfV2.png" alt="image"/>
<br/>

Set the capabilities of dumpcap to allow packet capture for the wireshark group members:
<br/>
*'sudo setcap 'CAP_NET_RAW+eip CAP_NET_ADMIN+eip' /usr/bin/dumpcap'*

<img src="https://i.imgur.com/K86iRzi.png" alt="image"/>
<br/>

Add your user to the wireshark group:
<br/>
*'sudo usermod -a -G wireshark $USER'*
<br/>
After adding yourself to the group, you need to log out and log back in for the group membership to take effect.

<img src="https://i.imgur.com/xCGqOHI.png" alt="image"/>
<br/><br/>

<h3>Install Visual Studio Code</h3>

Snap is a package management system that makes it easy to install applications on any Linux distribution. Visual Studio Code is available as a snap package. Install the Snap Package Manager:
<br/>
*'sudo apt install snapd'*
<br/>
Once Snap is installed, you can install Visual Studio Code by running:
<br/>
*'sudo snap install code –classic'*

<img src="https://i.imgur.com/jdDxVur.png" alt="image"/>
<br/><br/>


<h2>tcpdump</h2>

Running tcpdump with ‘sudo tcpdump’, after opening a webpage, there is continuous network traffic and will capture packets indefinitely. Press CTRL + C to stop capturing traffic. 

<img src="https://i.imgur.com/3gqRYt1.png" alt="image"/>
<br/>

To access manual for tcpdump with all the options available:
<br/>
*'man tcpdump'*

<img src="https://i.imgur.com/FpvV4Ko.png" alt="image"/>
<br/>

Can limit capture to just 5 packets with the ‘-c 5’ option (at first, it is listening and does not capture packets until there is network traffic, to generate traffic I just need to refresh a webpage). 
<br/>
*'sudo tcpdump -c 5'*

<img src="https://i.imgur.com/ptrWGqE.png" alt="image"/>
<br/>

There are useful options to make it easier to read packets. One option is to pass ‘-#’ to number the packets:
<br/>
*'sudo tcpdump -c5 -#'*

<img src="https://i.imgur.com/QExOkAA.png" alt="image"/>
<br/>

Passing ‘-A’ will show packets in ASCII. Most of it will be encrypted so it is not readable:
<br/>
*'sudo tcpdump -c5 -#A'*

<img src="https://i.imgur.com/Fhy0JX4.png" alt="image"/>
<br/>

Another option is to show it in both hexadecimal and ASCII which makes it look cleaner:
<br/>
*'sudo tcpdump -c5 -#XX'*

<img src="https://i.imgur.com/nnata1X.png" alt="image"/>
<br/>

To get time in a human readable format use four t’s (YYYY-MM-DD HH:MM:SS.fraction_of_seconds):
<br/>
*'sudo tcpdump -c5 -#tttt'*

<img src="https://i.imgur.com/txP9fHa.png" alt="image"/>
<br/>

Using ‘-D’ option lists all network interfaces avail on this workstation. The 1st interface, enp0s3 is the virtual network card I am using. Using ‘-i enp0s3’ I can limit packets to only this interface.  
<br/>
*'sudo tcpdump -c5 -#XXtttt -I enp0s3'*

<img src="https://i.imgur.com/LkbPxj8.png" alt="image"/>
<br/>

To monitor network traffic going to and from example.com:
<br/>
*'sudo tcpdump -#XXtttt host example.com -c5'*
<br/>
Can observe traffic going to IP 93.184.216.34

<img src="https://i.imgur.com/08b7T6i.png" alt="image"/>
<br/>

I can also limit captures by port and host:
<br/>
*'sudo tcpdump -#XXtttt -c5 port 443 and host skyroute66.com'*

<img src="https://i.imgur.com/tl9Xify.png" alt="image"/>
<br/>

Filtering traffic from a source IP or destination IP is also an option by using ‘src’ or ‘dst’:
<br/>
*'sudo tcpdump src skyroute66.com'*
<br/>
*'sudo tcpdump dst skyroute66.com'*

<img src="https://i.imgur.com/oh3sCj9.png" alt="image"/>
<br/><br/>

<h2>Shell Script with Visual Studio Code</h2>

Now, I will create a shell script using Visual Studio Code and create a new file named watchdog.sh. I am going to type a script to get all incoming and outgoing traffic from skyroute66.com and capture 5 packets:
<br/>
*'sudo tcpdump -#XXtttt host skyroute66.com -c5'*
<br/>
Next, by right-clicking watchdog.sh to open in Integrated Terminal, I can list directory in long format to get view file permissions for watchdog.sh. 
<br/>
*'ls -la'*
<br/>
I can see that watchdog.sh is not executable, therefore I need to change modes to allow executable permissions. List directory again to confirm mode change wit ‘x’ for all groups. 
<br/>
*'chmod +x watchdog.sh'*
<br/>
'*ls -la'*

<img src="https://i.imgur.com/ghLgrYt.png" alt="image"/>
<br/>

When I run script, I get 5 packets, human-readable date, source, dest IP, and packet data in hexadecimal and ASCII. 

<img src="https://i.imgur.com/YGgBoux.png" alt="image"/>
<br/>

To save the packet for later analysis or to use another utility to read packet content in better format, then I need to dump the capture packets into a dump file. To do this, I need to use the option ‘-w’ for write followed by the filename with ‘.pcap’ extension to save file as:
<br/>
*'sudo tcpdump -#XXtttt host skyroute66.com -c5 -w captured.pcap'*
<br/>
Once I run the watchdog.sh script, captured.pcap is created and will capture 5 packets. 

<img src="https://i.imgur.com/QGjIz0n.png" alt="image"/>
<br/>

When trying to read captured.pcap, it displays unreadable format. 

<img src="https://i.imgur.com/6JkPuRI.png" alt="image"/>
<br/>

Can try to read the captured.pcap in the Integrated Terminal by using the ‘-r’ option. Can also pass the same formatting options to it just as if it was freshly captured on the wire:
<br/>
*'sudo tcpdump -r captured.pcap'*
<br/>
*'sudo tcpdump -r captured.pcap -#XXtttt'*

<img src="https://i.imgur.com/7lUJXbm.png" alt="image"/>
<br/><br/>


<h2>Further Analysis with Wireshark</h2>
To look at this packet in a more user-readable format can use Wireshark. When I open the ‘captured.pcap’ file in Wireshark, can see it is in much more readable format. Can see there are 5 packets, with time, source IP, dest IP, Protocol, length, and info. It also breaks down the packet on the bottom and when you click on the hexadecimal/ASCII context, it displays in human-readable format on what it means. On the bottom when I click on the below packet content, I can see that it represents ‘Destination Port: 443’. 

<img src="https://i.imgur.com/zPsG5Si.png" alt="image"/>
<br/><br/>


<h2>File Rotation</h2>

To use option ‘-G’ and ‘-C’, I had to change directory permissions for where pcap were saved to allow permission to write for ‘other’ :
<br/>
chmod 0+w .

<img src="https://i.imgur.com/YOrvvc4.png" alt="image"/>
<br/>

The ‘-G’ option is used for rotation of dump files based on a specified timeout. This is useful when capturing packets for extended period of time and want to split the capture into multiple files based on duration. For example, if I wanted to capture packets to a new file every 2 minutes (600 seconds), I would use the ‘-w’ and ‘-G’ option like this:
<br/>
*'sudo tcpdump -#XXtttt -w capture-%Y-%m-%d_%H:%M:%S.pcap -G 120'*
<br/>
This format would include the current date and time in the filename allowing series of capture files with timestamps in their name. 

<img src="https://i.imgur.com/fCs4Ljr.png" alt="image"/>
<br/>

To capture packets by size I used the ‘-C’ option to limit by MB. 
<br/>
*'sudo tcpdump -#XXtttt -w captured.pcap -C 1'*

<img src="https://i.imgur.com/vp7gGuA.png" alt="image"/>
<br/>

Can also combine ‘-G’ and ‘-C’ to rotate by time or filesize:
<br/>
*'sudo tcpdump -#XXtttt -w logs.pcap -C 1 -G 15'*
<br/>
In command above, file will not exceed 1MB or 15 seconds.

<img src="https://i.imgur.com/yKXyLLc.png" alt="image"/>
<br/>

Will use expressions to filter through packets. Create a new file named advanced.sh and again add ‘-x’ executable permissions. Will write the following script:
<br/>
*'sudo tcpdump -#XXtttt ‘tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420’'*
<br/>
This will capture all the TCP traffic for a GET operation. There are many advanced expressions that can assist with analysis which can be found from resources online. 

<img src="https://i.imgur.com/PF023fi.png" alt="image"/>
