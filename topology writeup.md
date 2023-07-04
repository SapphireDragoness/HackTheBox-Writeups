***Topology Writeup***
I start by enumerating the network, finding two open ports (22 and 80).
[screenshot]
Snooping around the website, I find a subdomain called latex.topology.htb. Maybe there are others...
I search for subdomains using wfuzz and find to of them: stats.topology.htb and dev.topology.htb. The first contains server stats adn the second asks me to authenticate. 
[screenshot]
I try using some weak passwords, but it won't let me in (someone is taking security "seriously", yay!).
Going back to latex.topology.htb, I use LaTex injection to show the contents of /etc/passwd (https://book.hacktricks.xyz/pentesting-web/formula-doc-latex-injection#latex-injection).
[screenshot]
It works! There's a user called vdaisley. I can dump the passwords used for server authentication (contained in /var/www/dev/.htpasswd) using the same trick.
[screenshot]
It's encrypted (using Apache MD5), so let's use Hashcat (code 1600) to decrypt it.
[screenshot]
The password is "calculus20". Since password reuse is common, I use it to ssh into the machine.
User flag acquired!
[screenshot]
I upload linpeas (https://github.com/carlospolop/PEASS-ng) onto the machine using a simple http server (python3 -m http.server) to search for exploitable processes/binaries. I find the progam "gnuplot", which is exploitable (https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/gnuplot-privilege-escalation/).
I have permissions to write in the /opt/gnuplot directory, so I can place a .plt file there, changing the permissions of /bin/bash, and become root.
[screenshot]
Done!