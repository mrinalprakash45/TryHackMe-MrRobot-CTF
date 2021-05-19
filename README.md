
                            __  __   ____        ____     ___    ____     ___    _____ 
                           |  \/  | |  _ \      |  _ \   / _ \  | __ )   / _ \  |_   _|
                           | |\/| | | |_) |     | |_) | | | | | |  _ \  | | | |   | |  
                           | |  | | |  _ <   _  |  _ <  | |_| | | |_) | | |_| |   | |  
                           |_|  |_| |_| \_\ (_) |_| \_\  \___/  |____/   \___/    |_|  
                                                                      


```10.10.165.24```

1. Started with an Nmap scan. Nmap reveals 3 ports, 2 of which are opened (http and https). SSH seems to be closed.

2. Let’s start with the web server. Assisted by the hint, let’s get the robots.txt file. It discloses 2 hidden files, 1 of which being the key 1.
The second file is a dictionary, that we will probably need to use for the discovery of other locations.

3. Wordpress is installed. Moreover, the directory /license discloses credentials:
   elliot:ER28-0652

4. We login using the above credentials as administrators. As we are administrators, we can modify the templates. Go to Appearance > Editor and edit the first template (404.php) by replacing the PHP code with a reverse shell 

5. And visit http://10.10.165.24/404.php to open the reverse shell.

6. We are also provided with the MD5 hash of Mr Robot’s password:

$ cat password.raw-md5
cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
This hash was found and the associated password is abcdefghijklmnopqrstuvwxyz. Let’s try to log in as robot.

7. Our last key is very likely in the /root directory, and we will need a privilege escalation to access it.

The nmap scan reveals that the port 22 (ssh) is closed, probably because the service is not started. We would need to elevate our privileges.

Unfortunately, our user robot is not in the sudoers:

$ sudo -l
sudo -l
[sudo] password for robot: abcdefghijklmnopqrstuvwxyz

Sorry, user robot may not run sudo on linux.

8. OK, let’s find what programs we have with the SETUID bit set owned by root

9. Interestingly, nmap is on the list (it’s also the hint BTW). Besides, it’s a very old release (3.81)

$ which nmap
which nmap
/usr/local/bin/nmap
$ nmap --version
nmap --version

nmap version 3.81 ( http://www.insecure.org/nmap/ )
As described here, nmap is its older release (2.02 to 5.21) had an interactive mode which allows to execute commands.

Besides, nmap has the SETUID bit set, which means that we will be able to run commands as root:

$ ls -l /usr/local/bin/nmap
ls -l /usr/local/bin/nmap
-rwsr-xr-x 1 root root 504736 Nov 13  2015 /usr/local/bin/nmap
Let’s start nmap in interactive mode

10. We then move to the root folder to get our last key value.