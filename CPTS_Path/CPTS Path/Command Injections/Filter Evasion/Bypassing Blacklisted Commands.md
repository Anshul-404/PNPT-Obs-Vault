- A command blacklist usually consists of a set of words, and if we can obfuscate our commands and make them look different, we may be able to bypass the filters.

## Commands Blacklist
---
- So, let us go back to our very first payload and **re-add the `whoami` command** to see if it gets executed:
	- ![[Pasted image 20260326001924.png]]
- A basic command blacklist filter in `PHP` would look like the following:
```php
	$blacklist = ['whoami', 'cat', ...SNIP...];
	foreach ($blacklist as $word) {
	    if (strpos('$_POST['ip']', $word) !== false) {
	        echo "Invalid input";
	    }
	}
```
- However, this code i**s looking for an exact match of the provided command,** so if we send a slightly different command, it may not get blocked.
- Luckily, **we can utilize various ==obfuscation techniques==** that will execute our command without using the exact command word.

## Linux & Windows
---
- One very common and easy obfuscation technique is inserting **==certain characters within our command that are usually ignored by command shells==** like `Bash` or `PowerShell` and will execute the same command as if they were not there.
- Some of these characters are a **==single-quote `'` and a double-quote `"`,==** in addition to a few others.
- For example, if we want to obfuscate the `whoami` command, we can insert single quotes between its characters, as follows:
	- `w'h'o'am'i`
	![[Pasted image 20260326002455.png]]
- The important things to remember are that **==`we cannot mix types of quotes` and `the number of quotes must be even`.==**
- ![[Pasted image 20260326002612.png]]

## Linux Only
---
- We can insert a **few other Linux-only characters** in the middle of commands, and the `bash` shell would ignore them and execute the command.
- These characters **==include the backslash `\` and the positional parameter character `$@`==**
- This works exactly as it did with the quotes, but in this case, **==`the number of characters do not have to be even`==**, and we can insert just one of them if we want to:
```
	who$@ami
	w\ho\am\i
```

## Windows Only
---
- There are also some Windows-only characters we can insert in the middle of commands that do not affect the outcome, **==like a caret (`^`) character==**, as we can see in the following example:
	- ![[Pasted image 20260326003000.png]]

=> Question's answer: `ip=127.0.0.1%0ac'a't${IFS}${PWD:0:1}home${PWD:0:1}1nj3c70r${PWD:0:1}flag.txt`