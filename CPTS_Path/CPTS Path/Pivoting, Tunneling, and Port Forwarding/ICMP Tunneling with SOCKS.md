- ICMP tunneling encapsulates your traffic within `ICMP packets` containing `echo requests` and `responses`.
- ICMP tunneling would **only work when ping responses are permitted within a firewalled network.**
- We will use the **[ptunnel-ng](https://github.com/utoni/ptunnel-ng) tool to create a tunnel between our Ubuntu server and our attack host**. Once a tunnel is created, we will be able to proxy our traffic through the `ptunnel-ng client`.

# Setting Up & Using ptunnel-ng
--- 
- `git clone https://github.com/utoni/ptunnel-ng.git`
- `sudo apt install automake autoconf -y`
- `cd ptunnel-ng`
- `sudo ./autogen.sh `

- After running autogen.sh, **ptunnel-ng can be used from the client and server-side**. We will now need t**o transfer the repo from our attack host to the target host.**

## Transferring Ptunnel-ng to the Pivot Host
- `scp -r ptunnel-ng ubuntu@10.129.202.64:~/`
## Starting the ptunnel-ng Server on the Target Host
- `sudo ./ptunnel-ng -r10.129.202.64 -R22`

- The IP address following `-r` should be the **IP of the jump-box we want ptunnel-ng to accept connections on.**
- In this case, whatever IP is reachable from our attack host would be what we would use.

- Back on the **attack host, we can attempt to connect to the ptunnel-ng server (`-p <ipAddressofTarget>`) but ensure this happens through local port 2222** (`-l2222`).

## Connecting to ptunnel-ng Server from Attack Host
- `sudo ./ptunnel-ng -p10.129.202.64 -l2222 -r10.129.202.64 -R22`
- With the ptunnel-ng ICMP tunnel successfully established, we can attempt to connect to the target using SSH through local port 2222 (`-p2222`).

## Tunneling an SSH connection through an ICMP Tunnel
- `ssh -p2222 -lubuntu 127.0.0.1`

# Enabling Dynamic Port Forwarding over SSH
---
- `ssh -D 9050 -p2222 -lubuntu 127.0.0.1`
- We could use proxychains with Nmap to scan targets on the internal network (172.16.5.x). Based on our discoveries, we can attempt to connect to the target.