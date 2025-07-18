# Using wget
# Using SCP
# Using Base64
---
- In some cases, we may not be able to transfer the file. For example, **the remote host may have ==firewall protections**== that prevent us from downloading a file from our machine.
- In this type of situation, **we can use a simple trick to ==[base64](https://linux.die.net/man/1/base64) encode**== the file into `base64` format, and then we can paste the `base64` string on the remote server and decode it.

- For example, if we wanted to transfer a **binary file called `shell`**, we can `base64` encode it as follows:
	- `base64 shell -w 0`

- Now, **we can copy this `base64` string, go to the remote host, and use `base64 -d` to decode it, and pipe the output into a file:**
	- `echo f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU | base64 -d > shell`

# Validating File Transfers
---
- `file shell`
- `md5sum shell`