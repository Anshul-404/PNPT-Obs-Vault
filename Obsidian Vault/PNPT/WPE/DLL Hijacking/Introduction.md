# What is DLL?
---
- **D**ynamic **L**ink **L**ibrary
- Kind of an executable (not directly though)
- Similar to `.sl`files in Linux (shared libraries)
- Contains classes, functions, resources, variables, etc
- They **run with executables**
- **When Windows starts up a service or app, it looks for DLL.**
	- **==If it doesn't exist and any of the paths where it is searched for is writable, we can get malicious with it.==**