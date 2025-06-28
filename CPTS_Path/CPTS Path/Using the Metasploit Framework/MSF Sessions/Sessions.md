- MSFconsole **can manage multiple modules at the same time**. This is one of the many reasons it provides the user with so much flexibility. 
- This is done with the use of `Sessions`, **which creates dedicated control interfaces for all of your deployed modules.**
- Once several sessions are created, we can switch between them and link a different module to one of the backgrounded sessions to run on it or turn them into jobs.

# Using Sessions
---
- We can b**ackground the session either by pressing the `[CTRL] + [Z]` key** combination **or by typing the `background`** command in the case of Meterpreter stages.

 - Listing Active Sessions: `sessions`
 - Interacting with a Session: `sessions -i 1`

# Jobs
---
- If, for example, we are running **an active exploit under a specific port and need this port for a different module**, we cannot simply terminate the session using `[CTRL] + [C]`. 
- If we did that, **we would see that the port would still be in use,** affecting our use of the new module.
- We would need to **use the `jobs` command to look at the currently active tasks running in the background** and terminate the old ones to free up the port.

- Viewing the Jobs Command Help Menu:
	- `jobs -h`
- Viewing the Exploit Command Help Menu:
	- When we run an exploit, **we can run it as a job by typing** 
		- `exploit -j` => Running an Exploit as a Background Job
		- ![[Pasted image 20250628064416.png]]
	- Listing Running Jobs:
		- `jobs -l`