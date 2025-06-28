- Plugins are **readily available software** that has already been released by third parties and have given approval to the creators of Metasploit to integrate their software inside the framework.
- They can be useful for **automating repetitive tasks, adding new commands** to the `msfconsole`, and extending the already powerful framework.

# Using Plugins
---
- Navigating to `/usr/share/metasploit-framework/plugins`,  should show us which plugins we have to our availability:
	- ![[Pasted image 20250628061701.png]]
- If the plugin is found here, **we can fire it up inside `msfconsole` and will be met with the greeting outpu**t for that specific plugin:
	- ![[Pasted image 20250628061726.png]]
- If the plugin is not installed correctly, **we will receive the following error upon trying to load it.** :
	- ![[Pasted image 20250628061751.png]]

# Installing new Plugins
---
- To install new custom plugins not included in new updates of the distro, we can **take the .rb file provided on the maker's page and place it in the folder at `/usr/share/metasploit-framework/plugins` with the proper permissions.**
	- ![[Pasted image 20250628061846.png]]
	- ` sudo cp ./Metasploit-Plugins/pentest.rb /usr/share/metasploit-framework/plugins/pentest.rb`

# Mixins
---
Mixins are one of those features that, when implemented, offer a large amount of flexibility to both the creator of the script and the user.
Mixins are classes that act as methods for use by other classes without having to be the parent class of those other classes. Thus, it would be deemed inappropriate to call it inheritance but rather inclusion. They are mainly used when we:

1. Want to provide a lot of optional features for a class.
2. Want to use one particular feature for a multitude of classes.