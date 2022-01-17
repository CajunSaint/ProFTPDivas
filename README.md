# ProFTPDivas
We will exploit a machine using the ProFTPD 1.3.3c  exploit, add defenses to that machine, and regain access with the same exploit.

Link to Capstone project on YouTube: <iframe width="560" height="315" src="https://www.youtube.com/embed/B0fTzUi4_bQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Hey everyone, We are Team ProFTPDivas and our team members are, Andrea Martin, Samantha Aplicador, and Brooke Womack. We are going to show you how we were able to exploit the same machine, twice. First, we will use the known ProFTPD 1.3.3c exploit, to gain backdoor access on our victim’s machine. Next, we will add firewall defenses to protect the vulnerable machine. Finally, we will circumvent these defenses with an ssh tunnel and a cron job, so we can run the exploit again without being detected.

To begin, we first ran an nmap scan to determine open ports, ports 21, 22, and 80 were open. Next, we ran a nessus scan on the machine and discovered it contained a High Vulnerability with ProFTPD Compromised Source Packages. And below we can see its exploitable with Metasploit ProFTPD Backdoor command execution. 

We used Metasploit to search for proftpd exploits and selected option 5,  the backdoor exploit. We set the Remote HOSTS as the victim’s machine with the ip address ending in 105, we set the PAYLOAD as cmd/unix/reverse from looking at the options, and set the Listening HOST as our Kali machine with the ip address ending in 106. We then ran the exploit…and let Metasploit do its Metasploit thing.. and gained root access, you’ll see that by our command whoami.

As root, we concatenated the /etc/shadow file to gain the hash for the user marlinspike. We used John the Ripper to crack that hash, and obtain his password. Once we had the username and password for marlinspike, we were able to gain access into the victim’s machine using ssh.

We then created a new user, marvinspike, using the sudo adduser command.  We then gave our user admin privileges and switched users to marvinspike. Having a new user with administrative privileges, provides us future access to change/modify files in the victim's machine.

We next set up an SSH backdoor to gain persistence on our victim’s machine. By running ssh-keygen in the victim’s machine we created a public and private key pair and copied the key onto our kali machine and saved it as authorized_keys in the .ssh folder. By obtaining the corresponding private key, this provides the attacker with direct passwordless access into the victim’s machine.

The victim will likely try to block our attacker’s ip address on the FTP port, denying us access via ssh. To avoid that, we set up a cron job on the victim’s machine to open the ssh tunnel, every minute, to bypass the victim’s firewall rule.  We confirmed this by checking our listening port, on port 8888 on our kali machine using netstat -l. 

We next set up a firewall defense from the victim’s machine, using iptables to block traffic on port 21 from the attacker’s machine to test our cron job. We then verified the firewall was successfully set up with iptables -l , and it was.

Finally, we ran the Proftpd backdoor exploit again, this time we set our Remote & Listening HOST as our attacker machine ending in .106 and the Remote PORT to 8888.  These settings directly correlate with our crontab previously set up. We used the same payload from the previous exploit and as you can see, we successfully regained root access.

Sources:
https://infosecwriteups.com/basic-pentesting-1-walkthrough-vulnhub-4dac91b416ff
http://www.proftpd.org/
http://kb.ictbanking.net/article.php?id=667
https://www.aldeid.com/wiki/Exploits/proftpd-1.3.3c-backdoor
https://raymii.org/s/tutorials/Autossh_persistent_tunnels.html
https://www.ssh.com/academy/ssh/tunneling/example


