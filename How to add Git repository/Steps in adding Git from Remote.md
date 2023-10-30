1. Go to specific directory and fork the code in your repository
2. Go to /opt/<specific folder> then git init
3. Initialize your remote connection by adding it
4. Add ssh id to be able to fetch, need to execute evaluate for the agent to run. It will return you pid number

```
┌──(kali㉿kali)-[~]
└─$ eval `ssh-agent -s`
Agent pid 178439

┌──(kali㉿kali)-[~]
└─$ ssh-add ~/.ssh/id_rsa
/home/kali/.ssh/id_rsa: No such file or directory

┌──(kali㉿kali)-[~]
└─$ cd /opt/breach-parse

┌──(kali㉿kali)-[/opt/breach-parse]
└─$ sudo git fetch graceremote
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
```
6. You will see identity added
```
┌──(kali㉿kali)-[~]
└─$ ssh-add ~/.ssh/id_ed25519
Identity added: /home/kali/.ssh/id_ed25519 (zabala_marygrace31@yahoo.com)
```
7. You can now execute fetch

