## Bypass Blacklisted Operators
---
- We will see that **most of the injection operators are indeed blacklisted**. 
- However, the **==new-line character is usually not blacklisted, as it may be needed in the payload==** itself.
- We know that the new-line character works in appending our commands both in Linux and on Windows, so let's try using it as our injection operator:
	- ![[Pasted image 20260316013201.png]]
- As we can see, even though our payload did include a new-line character, **our request was not denied.**
- Let us start by discussing how to bypass a commonly blacklisted character - a space character.

## Bypass Blacklisted Spaces
---
- Now that we have a working injection operator, let us **modify our original payload and send it again as (`127.0.0.1%0a whoami`):**
	- ![[Pasted image 20260316013252.png]]
- As we can see, we still get an `invalid input` error message, **==meaning that we still have other filters to bypass.==**
- So, as we did before, let us only add the next character (which is a space) and see if it caused the denied request:
	- ![[Pasted image 20260318015258.png]]

**Using Tabs**
- Using **==tabs (%09) instead of spaces is a technique that may work==**, as both Linux and Windows accept commands with tabs between arguments, and they are executed the same. 
- So, let us try to **use a tab instead of the space character** (`127.0.0.1%0a%09`) and see if our request is accepted:
	- ![[Pasted image 20260318015409.png]]
- As we can see, we successfully **bypassed the space character filter by using a tab** instead.

**Using $IFS**
- Using the **==($IFS) Linux Environment Variable may also work since its default value is a space and a tab==**, which would work between command arguments. 
- So, if we **use ${IFS} where the spaces should be**, the variable should be **automatically replaced with a space**, and our command should work:
	- ![[Pasted image 20260318015520.png]]

**Using Brace Expansion**
- There are many other methods we can utilize to bypass space filters. 
- For example, we **can use the `Bash Brace Expansion` feature, which automatically adds spaces** between arguments wrapped between braces, as follows:
	- ![[Pasted image 20260318015601.png]]
- As we can see, the command was successfully executed without having spaces in it. We can utilize the same method in command injection filter bypasses, by using brace expansion on our command arguments, like (`127.0.0.1%0a{ls,-la}`
- To discover more space filter bypasses, **==check out the [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space) page on writing commands without spaces==**.
# Assigment
---
- Use ${IFS}