# Assessment
---
- `sudo vi /etc/php/7.4/apache2/php.ini`
- ![[Pasted image 20251105072121.png]]
- `sudo su`
- `cd /var/www/html`
- `sudo echo "<?php system('id') ?>" > shell.php`
- Access `shell.php` from webpage
- Read the `error.log` file `/var/log/apache2/error.log`
	- ![[Pasted image 20251105072742.png]]