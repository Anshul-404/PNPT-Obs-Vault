- **Hunting for credentials is one of the first steps once we have access to the system.**
- There are several sources that can provide us with credentials that we put in four categories. These include, but are not limited to:
	- **==`Files`==** including **configs, databases, notes, scripts, source code, cronjobs, and SSH keys**
	- `History` including ==**logs, and command-line history**==
	- `Memory` including **cache, and in-memory processing**
	- ==**`Key-rings`== such as browser stored credentials**

- We should adapt our **==approach to the circumstances of the environment==** and keep the big picture in mind.
- it is crucial to keep in mind **==how the system works, its focus, what purpose it exists for, and what role it plays in the business logic==** and the overall network.

# Files
---
- One core principle of Linux is that everything is a file.
- These categories are the following:
	- **Configuration files**
	- **Databases**
	- **Notes**
	- **Scripts**
	- **Cronjobs**
	- **SSH keys**
- Usually, the configuration files are marked with the following ==**three file extensions (`.config`, `.conf`, `.cnf`).**==

## Searching for configuration files
- `for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done`
  
- Another option is to run the **scan directly for each file found with the specified file extension and output the contents.**
- In this example, we search for **three words (`user`, `password`, `pass`)** in each file with the **file extension `.cnf`.**:
	- `for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done`
	  
## Searching for databases
- `for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done`

## Searching for notes
- `find /home/* -type f -name "*.txt" -o ! -name "*.*"`
## Searching for scripts
- Among other things, **these also contain credentials that are necessary to be able to call up and execute the processes automatically**
- `for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done`

## Enumerating cronjobs
- These are divided into the s**ystem-wide area (`/etc/crontab`) and user-dependent executions**
- Furthermore, there are the areas that are divided into different time ranges (`/etc/cron.daily`, `/etc/cron.hourly`, `/etc/cron.monthly`, `/etc/cron.weekly`).
- The scripts and files used by `cron` can also be **found in `/etc/cron.d/` for Debian-based distributions.**
	- `cat /etc/crontab`
	- `crontab -l`

## Enumerating history files
- We are interested in the files that store **users' command history and the logs** that store information about system processes.
- In the history of the commands entered on Linux distributions that use Bash as a standard shell, **we find the associated files in `.bash_history`**
	- `tail -n5 /home/*/.bash*`

## Enumerating log files
- The entirety of log files can be divided into four categories:
	- Application logs
	- Event logs
	- Service logs
	- System logs
- These can vary depending on the applications installed, but here are some of the most important ones:
	- ![[Pasted image 20250701064924.png]]
- Here are some strings we can use to find interesting content in the logs:
	- `for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done`

# Memory and cache
----
## Mimipenguin
- Many applications and processes work with **credentials needed for authentication and store them either in memory or in files so that they can be reused.**
- In order to retrieve this type of information from Linux distributions, **==there is a tool called [mimipenguin](https://github.com/huntergregal/mimipenguin) that makes the whole process easier==**:
	- `sudo python3 mimipenguin.py`

## LaZagne
- This tool allows us to access far more resources and extract the credentials. 
- The passwords and hashes we can obtain come from the following sources but are not limited to:
	- ![[Pasted image 20250701065134.png]]
- For example, **==`Keyrings` are used for secure storage and management of passwords on Linux distributions.==** 
- Passwords are stored encrypted and protected with a master password.
	- `sudo python2.7 laZagne.py all`

## Browser credentials
- Browsers store the passwords saved by the user in an encrypted form locally on the system to be reused.
- For example, when we store credentials for a web page in the **==Firefox browser, they are encrypted and stored in `logins.json`==** on the system:
	- `ls -l .mozilla/firefox/ | grep default`
	- `cat .mozilla/firefox/1bplpd86.default-release/logins.json | jq .`

- The tool **==[Firefox Decrypt](https://github.com/unode/firefox_decrypt) is excellent for decrypting these credentials, and is updated regularly.==**
	- `python3.9 firefox_decrypt.py`
- Alternatively, **==`LaZagne` can also return results if the user has used the supported browser.==**