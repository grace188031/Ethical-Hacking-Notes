#smbrelaymitigation
## SMB Relay Mitigation

- enable SM signing speed for file shares will be slow but smb signing still use as as security as a trade-off for the speed
- Disable NTLM Authentication. It stops attack. If Kerberos stops working(main authentication), it will go back to NTLM and attacks will be viable again
- Account tiering - Limits domain admins to specific tasks. COns is Enforvcing the policy may be difficult
- Local Admin restriction - Can prevent a lot of lateral movement and Con is potential increase in the amount of service desk ticket

![[Pasted image 20240714231216.png]]