# HTTP/S
---
- There is nothing worse than being on a penetration test, and a client's network **IDS picks up on a sensitive file being transferred over plaintext and having them ask** why we sent a password to our cloud server without using encryption.

# Nginx - Enabling PUT
---
- A good alternative for transferring files to `Apache` is [Nginx](https://www.nginx.com/resources/wiki/) because the configuration is less complicated, and the module system does not lead to security issues as `Apache` can.
- When allowing `HTTP` uploads, **it is critical to be 100% positive that users cannot upload web shells and execute them.**

- Create a Directory to Handle Uploaded Files:
	- `sudo mkdir -p /var/www/uploads/SecretUploadDirectory`
- Change the Owner to www-data:
	- `sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory`
- Create Nginx Configuration File:
- Create the Nginx configuration file by creating the file `/etc/nginx/sites-available/upload.conf` with the contents:
```nginx
	server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```

- Symlink our Site to the sites-enabled Directory:
	- `sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/`
- Start Nginx:
	- `sudo systemctl restart nginx.service`
If we get any error messages, **check `/var/log/nginx/error.log`.** 
![[Pasted image 20250623064933.png]]

- Remove NginxDefault Configuration:
	- `sudo rm /etc/nginx/sites-enabled/default`


- Upload File Using cURL:
	- `curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt`
	- `sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt `

- Once we have this working, a good test is to ensure the directory listing is not enabled by navigating to `http://localhost/SecretUploadDirectory`.
- By default, with `Apache`, if we hit a directory without an index file (index.html), it will list all the files.
- Thanks to `Nginx` being minimal, features like that are not enabled by default.