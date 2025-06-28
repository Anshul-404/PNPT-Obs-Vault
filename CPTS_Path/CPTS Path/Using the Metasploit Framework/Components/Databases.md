- `Databases` in `msfconsole` are used to keep track of your results.
- `Msfconsole` has built-in support for the PostgreSQL database system. 
- Database entries can also be used to configure Exploit module parameters with the already existing findings directly.

# Setting up the Database
---
- `sudo service postgresql status`
- Start PostgreSQL:
	- `sudo systemctl start postgresql`
- MSF - Initiate a Database: 
	- `sudo msfdb init`
- Sometimes an error can occur if Metasploit is not up to date. 
- This difference that causes the error can happen for several reasons. First, often it helps to update Metasploit again (apt update) to solve this problem. 

- `sudo msfdb status`

# MSF - Connect to the Initiated Database
---
- `sudo msfdb run`
- If, however, we already have the database configured and **are not able to change the password to the MSF username**, proceed with these commands:
	- `msfdb reinit`
	- `cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/`
	- `sudo service postgresql restart`
	- `msfconsole -q`
- The `msfconsole` **also offers integrated help for the database.** This gives us a good overview of interacting with and using the database.

# MSF - Database Options
---
- `help database`
	- ![[Pasted image 20250628060813.png]]

# Using the Database
---
## Workspaces
---
- We can think of **Workspaces the same way we would think of folders in a project.**
- We can **segregate the different scan results, hosts, and extracted information by IP, subnet, network, or domain.**
  
- To view the current Workspace list, use the `workspace` command. **Adding a `-a` or `-d` switch after the command, followed by the workspace's name, will either `add` or `delete` that workspace to the database**:
	- `workspace`
	- `workspace -a` => Add Workspace
	- `workspace -d` => Delete Workspace
- Type the `workspace [name]` command to switch the presently used workspace:
	- `workspace -a Target_1`
	- `workspace Target_1`
	  
- To see what else we can do with Workspaces, we can use the `workspace -h` command for the help menu related to Workspaces:
	- ![[Pasted image 20250628061142.png]]

# Importing Scan Results
---
- Next, let us assume we want to **import a Nmap scan of a host into our Database's Workspace** to understand the target better:
	- `db_import Target.xml`

# Using Nmap Inside MSFconsole
---
- `db_nmap -sV -sS 10.10.10.8`

## Data Backup
---
- After finishing the session, make sure to **back up our data** if anything happens with the PostgreSQL service. To do so, use the db_export command:
	- `db_export -h`
	- `db_export -f xml backup.xml`
- This data can be imported back to msfconsole later when needed.

# Hosts
---
- The `hosts` command **displays a database table automatically populated with the host addresses, hostnames, and other information** we find about these during our scans and interactions:
	- ![[Pasted image 20250628061437.png]]

# Services
---
- The services command functions the same way as the previous one. It contains a **table with descriptions and information on services discovered during scans** or interactions.

# Credentials
---
- The creds command allows you to **visualize the credentials gathered during your interactions with the target host.** 
- We can also add credentials manually, match existing credentials with port specifications, add descriptions, etc.

# Loot
---
- The loot command works in **conjunction** with the command above to **offer you an at-a-glance list of owned services and users.** 
- The loot, in this case, refers to **hash dumps from different system types, namely hashes, passwd, shadow, and more.**