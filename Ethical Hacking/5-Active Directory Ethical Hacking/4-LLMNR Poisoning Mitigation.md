
1. Disable LLMNR and NBT-NS
	- For LLMNR, turn OFF Multicast Name Resolution in Group policy editor
		- Go to Local Computer Policy/Group Policy
		- Computer Configuration
		- Administrative Templates
		- Network
		- DNS Client 
	- Fot NBT-NS, in Group Policy Editor
		- Navigate Network Connections
		- Network Adapter Properties
		- TCP/IPV4 Properties
		- Advanced Tab
		- WINS Tab -> select "Disable NetBIOS over TCP/IP"
2. If a company must use or cannot disable the NBT-NS/LMNR. The best action to do is to:
	- Require Network Access Control
	- Require a strong user password. The longer the user password, the harder it is to crack
