- In some instances, we may be dealing with advanced filtering solutions, like **==Web Application Firewalls (WAFs), and basic evasion techniques may not necessarily work.==**
- We can utilize more advanced techniques for such occasions, which make detecting the injected commands much less likely.

# Case Manipulation
---
- One command obfuscation technique we can use is case manipulation, like **==inverting the character cases of a command (e.g. `WHOAMI`) or alternating between cases (e.g. `WhOaMi`)==**
- This usually works because a command blacklist may not check for different case variations of a single word, as Linux systems are case-sensitive.

- In **Windows**, commands for **==PowerShell and CMD are case-insensitive==**, meaning they will execute the command regardless of what case it is written in:
	- ![[Pasted image 20260326005618.png]]
- However, when it comes to Linux and a bash shell, which are case-sensitive,we have to find a command that **turns the command into an all-lowercase word.** ==**One working command we can use is the following:**==
	- ![[Pasted image 20260326010050.png]]
- As we can see, the command did work, even though the word we provided was (`WhOaMi`). This **==command uses `tr` to replace all upper-case characters with lower-case==** characters.
- However, if we try to use the above command with the `Host Checker` web application, **we will see that it still gets blocked:**
	- ![[Pasted image 20260326010132.png]]
- It is because the command **==above contains spaces, which is a filtered character in our web application, as we have seen before.==** So, with such techniques, **`we must always be sure not to use any filtered characters`**, otherwise our requests will fail, and we may think the techniques failed to work.
	- Once we replace the spaces with tabs (`%09`), we see that the command works perfectly:
	- ![[Pasted image 20260326010313.png]]
- There are many other commands we may use for the same purpose, like the following:
	- `$(a="WhOaMi";printf %s "${a,,}")`

# Reversed Commands
---
- In this case, we will be w**riting `imaohw` instead of `whoami` to avoid triggering** the blacklisted command.
- We can get **creative** with such techniques and ==**create our own Linux/Windows commands**== that eventually execute the command without ever containing the actual command words.
- First, we'd have to **get the reversed string of our command in our terminal**, as follows:
	- ![[Pasted image 20260326011458.png]]
	- Then, we can execute the **original command by reversing it back in a sub-shell** (`$()`), as follows:
	- ![[Pasted image 20260326011510.png]]
- We see that even though the command does not **contain the actual `whoami` word, it does work** the same and provides the expected output.
- ![[Pasted image 20260326011602.png]]
- Tip: If you wanted to **bypass a character filter with the above method, you'd have to reverse them as well**, or include them when reversing the original command.
- The same can be applied in `Windows.` We can first reverse a string, as follows:
	- ![[Pasted image 20260326011724.png]]
	- `"whoami"[-1..-20] -join ''`
	- ` iex "$('imaohw'[-1..-20] -join '')"`

# Encoded Commands
---
- The final technique we will discuss is helpful for commands containing filtered characters or characters that may be URL-decoded by the server.
- This may **allow for the command to get ==messed up by the time it reaches the shell==** and eventually fails to execute.
- Instead of copying an existing command online, **==we will try to create our own unique obfuscation command==** this time.
- The command we create will be unique to each case, depending on what characters are allowed and the level of security on the server.

- We can utilize various encoding tools, **==like `base64` (for b64 encoding) or `xxd` (for hex encoding)==**. Let's take `base64` as an example. First, we'll encode the payload we want to execute (which includes filtered characters):
	- ![[Pasted image 20260327003132.png]]
- Now we can **==create a command that will decode the encoded string in a sub-shell==** (`$()`), and then pass it to `bash` **to be executed (i.e. `bash<<<`), as follows:**
	- `bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)`
	- ![[Pasted image 20260327003334.png]]
- **==Tip==**: Note that **==we are using `<<<` to avoid using a pipe `|`, which is a filtered character==**.
	![[Pasted image 20260327003553.png]]
- Even **==if some commands were filtered, like `bash` or `base64`==**, we could **bypass that filter** with the **==techniques we discussed in the previous section (e.g., character insertion),==** or use other **alternatives like `sh` for command execution and `openssl` for b64 decoding**, or `xxd` for hex decoding.

- We use the same technique with **Windows** as well. First, we need to base64 encode our string, as follows:
	- `[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))`
	- ![[Pasted image 20260327003713.png]]
- Finally, we can decode the b64 string and execute it with a PowerShell sub-shell (`iex "$()"`), as follows:
	- ![[Pasted image 20260327003725.png]]
	- `iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"`
- As we can see, we can get creative with `Bash` or `PowerShell` and create new bypassing and obfuscation methods that have not been used before, and hence are very likely to bypass filters and WAFs. **==Several tools can help us automatically obfuscate our commands,==** which we will discuss in the next section.
- In addition to the techniques we discussed, we can utilize numerous other methods, like wildcards, regex, output redirection, integer expansion, and many others. **We can find some such techniques on [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-with-variable-expansion).**

Q=> Find the output of the following command using one of the techniques you learned in this section: find /usr/share/ | grep root | grep mysql | tail -n 1
- `ip=127.0.0.1%0abash<<<$(base64${IFS}-d<<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDEK)`