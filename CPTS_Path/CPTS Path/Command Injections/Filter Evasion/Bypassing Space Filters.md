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
	- 