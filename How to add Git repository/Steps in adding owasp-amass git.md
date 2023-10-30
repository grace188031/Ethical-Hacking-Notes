
```
┌──(kali㉿kali)-[/opt/owasp-amass]
└─$ git remote add graceremotehttps://github.com:grace188031/amass.git
fatal: detected dubious ownership in repository at '/opt/owasp-amass'
To add an exception for this directory, call:

        git config --global --add safe.directory /opt/owasp-amass

┌──(kali㉿kali)-[/opt/owasp-amass]
└─$ git config --global --add safe.directory /opt/owasp-amass

┌──(kali㉿kali)-[/opt/owasp-amass]
└─$ git remote add graceremote https://github.com:grace188031/amass.git
error: could not lock config file .git/config: Permission denied
fatal: could not set 'remote.graceremote.url' to 'https://github.com:grace188031/amass.git'

┌──(kali㉿kali)-[/opt/owasp-amass]
└─$

```