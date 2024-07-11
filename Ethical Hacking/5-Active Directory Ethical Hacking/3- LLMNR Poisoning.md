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





