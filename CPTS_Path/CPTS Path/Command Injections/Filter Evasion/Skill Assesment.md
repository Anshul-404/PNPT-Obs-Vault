- Tried all sorts of operators only **==&&==** worked:
- Using normal commands always gave "malicious request", so **==obfuscate it with "" or ''==**
![[Pasted image 20260327204139.png]]
![[Pasted image 20260327204153.png]]


- **==NOTE==**: `&&` **==ONLY works when first command executes successfully==** **`||` works when first fails**
- Since only && is allowed, we have to make sure that **==mv command succeeds ALWAYS==**.
- So, we need a **new file each time to move.**
	- ![[Pasted image 20260327204802.png]]
	- Final payload: `GET /index.php?to=tmp%26%26c"a"t${IFS}${PATH:0:1}flag.txt&from=696212415.txt&finish=1&move=1 HTTP/1.1`