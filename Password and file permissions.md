Password and file permissions 

* Stored Passwords
	* history - might show passwords in the bash history 
	* cat .bash_history
	* root password123 in this example 
	* run linpeas and other scripts, they will do the searching and hash outputs for you most of the time 
* Escilation via weak file permissions 
	* do we have write permsision to something we shouldn't 
	* can we overwrite stuff?
* cat /etc/passwd
	* shows the users, groups, id, of users on the box 
	* does not have the actual password, thats in the /etc/passwd file 
	* if you delete the placeholder 'x' in the /etc/passwd file then that user does not have a password and you can just su user to get in 
		* need write access to the /etc/passwd file 
	* change id to just become the root user group

## Payload all the things 
* https://github.com/swisskyrepo/PayloadsAllTheThings
	* Sensitive files

		find / -name authorized_keys 2> /dev/null
		find / -name id_rsa 2> /dev/null
		
* find the private key 
* ssh -i key root@ip