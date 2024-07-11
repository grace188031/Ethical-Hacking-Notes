
1. 1 Windows Server and 2 Window Work Stations
2. 60 GB Disk Space
3. 16 GB RAM

Windows Server 2022 - 
IP Address: 192.168.64.250
Username: Administrator
Password: `P@$$w0rd!`

Service Principal Name: SQLService
Users:
Peter Parker - pparker

```
DistinguishedName : CN=Peter Parker,CN=Users,DC=MARVEL,DC=local
Enabled           : True
GivenName         : Peter
Name              : Peter Parker
ObjectClass       : user
ObjectGUID        : d21c33bd-54d9-43f3-9214-3a6699afd11d
SamAccountName    : pparker
SID               : S-1-5-21-3959242780-1137942380-88108419-1103
Surname           : Parker
UserPrincipalName : pparker@ -Path DC=marvel,DC=local

```

```

DistinguishedName : CN=Frank Castle,CN=Users,DC=MARVEL,DC=local
Enabled           : True
GivenName         : Frank
Name              : Frank Castle
ObjectClass       : user
ObjectGUID        : 65a7ff80-6bee-418a-bc5d-79dea5ac8cbc
SamAccountName    : fcastle
SID               : S-1-5-21-3959242780-1137942380-88108419-1104
Surname           : Castle
UserPrincipalName : fcastle@ -Path DC=marvel,DC=local

```

```
DistinguishedName : CN=Tony Stark,CN=Users,DC=MARVEL,DC=local
Enabled           : True
GivenName         : Tony
Name              : Tony Stark
ObjectClass       : user
ObjectGUID        : b6a03fcc-5608-48af-af9b-20a77f297a47
SamAccountName    : tstark
SID               : S-1-5-21-3959242780-1137942380-88108419-1105
Surname           : Stark
UserPrincipalName : tstark@ -Path DC=marvel,DC=local
```

```
DistinguishedName : CN=SQL Service,CN=Users,DC=MARVEL,DC=local
Enabled           : True
GivenName         : SQL
Name              : SQL Service
ObjectClass       : user
ObjectGUID        : 421822eb-b4ad-4d9c-a878-9cb10bb66529
SamAccountName    : sqlservice
SID               : S-1-5-21-3959242780-1137942380-88108419-1106
Surname           : Service
UserPrincipalName : sqlservice@ -Path DC=marvel,DC=local

```

Machine 1: Punisher
User: fcastle
password: `P@$$w0rd1`
Local Admin password: Administrator : `P@$$w0rd!`
Frank Castle: `P@$$w0rd1`
Pei Parker: `P@$$w0rd2`


Ip address: 192.168.64.220
Gateway: 192.168.64.1


Machine 2:
User: pparker
password: `P@$$w0rd2`
Local Admin password: `P@$$w0rd!`

Ip address: 192.168.64.221


