- [Sshuttle](https://github.com/sshuttle/sshuttle) is another tool written in Python which **removes the need to configure proxychains.**
- However, this tool only works for pivoting over SSH and **does not provide other options for pivoting over TOR or HTTPS** proxy servers.
- `Sshuttle` can be **extremely useful for automating the execution of iptables and adding pivot rules for the remote host**.
- We can configure the **Ubuntu server as a pivot point and route all of Nmap's network traffic with sshuttle** using the example later in this section.

- To use sshuttle, **we specify the option `-r` to connect to the remote machine with a username and password**. 
- Then we need to **include the network or IP we want to route through the pivot host,** in our case, is the network 172.16.5.0/23.

# Running sshuttle
---
- `sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 -v`
- `sshuttle 172.16.0.0/16 -r webadmin@10.129.229.129 --ssh-cmd "ssh -i id_rsa"` with id_rsa
- With this command, **sshuttle creates an entry in our `iptables` to redirect all traffic to the 172.16.5.0/23 network through the pivot host.**