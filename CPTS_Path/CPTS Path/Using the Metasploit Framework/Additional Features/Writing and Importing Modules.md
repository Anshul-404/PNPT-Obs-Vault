- [ExploitDB](https://www.exploit-db.com) is a great choice when searching for a custom exploit. We can use tags to search through the different exploitation scenarios for each available script. One of these tags is [Metasploit Framework (MSF)](https://www.exploit-db.com/?tag=3), which, if selected, will display only scripts that are also available in Metasploit module format.

# MSF - Loading Additional Modules at Runtime
---
- `cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb`
- `msfconsole -m /usr/share/metasploit-framework/modules/`

# MSF - Loading Additional Modules
---
- `loadpath /usr/share/metasploit-framework/modules/`

- Alternatively, we can also launch `msfconsole` and run the `reload_all` command for the newly installed module to appear in the list:
	- `reload_all`