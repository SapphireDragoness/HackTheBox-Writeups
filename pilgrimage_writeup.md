***Pilgrimage Writeup***
A quick nmap scan reveals two open ports, 22 (ssh) and 80 (webserver). It also reveals an exposed Git repository. Let's analyze it.
[screenshot]
I download the .git folder using git-dumper (https://github.com/arthaud/git-dumper).
By inspecting the files, I find a username (emily) and a 'magick' executable. 
This version of ImageMagick is vulnerable to Arbitrary File Read (https://www.exploit-db.com/exploits/51261). 
I also find the reference to a database file: databases mean passwords!
[screenshot]
Knowing this, I can generate a malicious image (using https://github.com/voidz0r/CVE-2022-44268) targeting the database and upload it to the site.
(cargo run "/var/db/pilgrimage")
The shrunken image contains a database dump: the contents can be inspected by converting the hex values into ascii.
[screenshot]
There's a password for user 'emily'. Since password reuse is common, I try ssh'ing into the machine using those credentials.
[screenshot]
User flag acquired!

I immediately notice an executable, pspy64: it's a process monitor. I execute it and find out that there is a script called "malwarescan.sh" currently running. Let's see what it does.
[screenshot]
[screenshot]
The script executes a program called "binwalk", which is vulnerable to remote command execution (https://www.exploit-db.com/exploits/51249).
I set up a listener, encrypt the payload into a png image using the above exploit, place it in the /var/www/pilgrimage.htb/shrunk directory and get back a shell, which has root access. Root flag!