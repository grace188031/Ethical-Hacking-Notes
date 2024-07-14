#smbrelayattack
#smbsigningdisabled
## SMB Relay Attacks Lab

1. Identify the host without SMB signing enabled
Command: `nmap script=smb2-security-mode.nse -p445 192.168.64.220 -Pn`

`-Pn` - Treats all hosts as online, skipping host discovery. This is useful if you know the host is up but it isn't responding to ICMP echo requests (ping).


