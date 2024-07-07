#github #gitaddfromremote


1. Go to specific directory and fork the code in your repository
2. Go to /opt/<specific folder> then git init
3. Initialize your remote connection by adding it
```
nano .git/config
[remote "graceremote"]
        url = https://github.com/grace188031/breach-parse.git
        fetch = +refs/heads/*:refs/remotes/graceremote/*
```
5. Add ssh id to be able to fetch, need to execute evaluate for the agent to run. It will return you pid number

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
7. You can now execute fetch and pull

```
┌──(kali㉿kali)-[/opt/breach-parse]
└─$ sudo git fetch graceremote
remote: Enumerating objects: 53, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 53 (delta 2), reused 4 (delta 1), pack-reused 43
Unpacking objects: 100% (53/53), 13.08 KiB | 101.00 KiB/s, done.
From https://github.com/grace188031/breach-parse
 * [new branch]      master     -> graceremote/master
┌──(kali㉿kali)-[/opt/breach-parse]
└─$ sudo git  pull graceremote master
From https://github.com/grace188031/breach-parse
 * branch            master     -> FETCH_HEAD

┌──(kali㉿kali)-[/opt/breach-parse]
└─$ ls
breach-parse.sh  install.sh  README.md

```

|  Stakeholder   |  Purpose   | Key Messages
| --- | --- |---|
|Vendor for hardware and software|Supply high-quality and reliable hardware and software solutions that meet the project's requirements and contribute to the successful execution and completion of the project.  | |
