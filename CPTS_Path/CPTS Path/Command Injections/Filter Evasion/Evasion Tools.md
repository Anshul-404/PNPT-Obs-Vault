- If we are dealing with **advanced security tools, we may not be able to use basic, manual obfuscation** techniques. In such cases, it may be best to resort to automated obfuscation tools. 
- This section will discuss a couple of **==examples of these types of tools, one for `Linux` and another for `Windows.`==**
## Linux (Bashfuscator)
---
- A handy tool we can utilize for **==obfuscating bash commands is [Bashfuscator](https://github.com/Bashfuscator/Bashfuscator)==**. We can clone the repository from GitHub and then install its requirements, as follows:
	- `git clone https://github.com/Bashfuscator/Bashfuscator`
	- `cd Bashfuscator`
	- `pip3 install setuptools==65`
	- `python3 setup.py install --user`

- There are many flags we can use with the tool to fine-tune our final obfuscated command, as we can see in the `-h` help menu:
	- ![[Pasted image 20260327011435.png]]
- We can **start by simply providing the command we want to obfuscate** with the `-c` flag:
	- `./bashfuscator -c 'cat /etc/passwd'`
	- ![[Pasted image 20260327011636.png]]

- We can use some of the flags from the help menu to **==produce a shorter and simpler obfuscated command,==** as follows:
	- `./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1`
	- ![[Pasted image 20260327011719.png]]
- We can now test the **outputted command with `bash -c ''`, to see whether it does execute** the intended command:
	- `bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'`

# Windows (DOSfuscation)
---
- There is also a very similar tool that we can use for **==Windows called [DOSfuscation](https://github.com/danielbohannon/Invoke-DOSfuscation)==**. Unlike `Bashfuscator`, this is an interactive tool, as **we run it once and interact with it to get the desired obfuscated command.** 
- We can once again clone the tool from GitHub and then invoke it through PowerShell, as follows:
```powershell  
	git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git
	cd Invoke-DOSfuscation
	Import-Module .\Invoke-DOSfuscation.psd1
	Invoke-DOSfuscation Invoke-DOSfuscation> help`
```
![[Pasted image 20260327012058.png]]
- We can even use `tutorial` to see an example of how the tool works. Once we are set, we can start using the tool, as follows:
	- ![[Pasted image 20260327012144.png]]
	- ![[Pasted image 20260327012154.png]]
- Tip: If we do not have access to a Windows VM, we can run the above code on a Linux VM through `pwsh`. Run `pwsh`, and then follow the exact same command from above. This tool is installed by default in your `Pwnbox` instance. You can also find installation instructions at this [link](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux).