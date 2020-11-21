System Enumeration Commands

* System enumeration 
	* hostname
	* uname -a 
		* show the kernel and os info
		* use to search for kernel exploits 
		* cat /proc/version
		* similar output 
		* cat /etc/issue	
		* lscup
			* good for multiple cores/threads, if you exploit needs serveral cores you'll need to check if they are there 
	* ps aux
		* show services that are running 
		* show services by user, find web servers or cron jobs
		* network file shares 
		* what user is running what task, what's running as root
		* ps aux | grep root 
* User enumeration 
	* who we are and what we can do
	* whoami
	* id
		* shows user id, useful to find your user id for ls -la stuff
	* sudo -l
		* what can we run as sudo
		* even with no password 
	* cat /etc/password
		* can see the users, passwords are shown in the /etc/shadow file
	* list just the users 
		* cat /etc/shadow | cut -d :-f 1 
	* cat /etc/shadow
	* cat /etc/group
	* history
* Network enumeration
	* ifconfig, works sometimes but depreacated 
	* ip -a is modern replacement 
	* ip route 
		* route to another network 
	* ip neigh 
		* similar to the arp tables of the past
		* see active connections 
	* netstat -ano
		* see open ports and connections over those ports 
		* ports only open to the localhost 
* Password hunting 
	* grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2> /dev/null
		* shows anywhere on the system that the word password is used an shows in red
	* locate password | more
		* search by files with the name password 
	* find / -name id_rsa 2> /dev/null
		* looking for ssh keys to move latterly on a system or network 