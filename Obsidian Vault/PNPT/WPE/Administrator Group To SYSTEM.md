# UAC Bypass from Administrators Group

**Need:**  
When you're in the `Administrators` group on Windows but still can't access certain directories (like `C:\Users\Administrator`) or perform admin actions, it's due to **User Account Control (UAC)** restricting your privileges. Even admins run with a limited token until elevated.

**==Note:- We can use meterpreter to load incognito and add new user if we have this group. Assign this new user to administrators group as well and then RDP for GUI==**

---

## 🧠 Why This Happens
Windows applies UAC to limit admin accounts until a manual or automated elevation is triggered. This is why you're denied access despite being in `Administrators`.

---

## ✅ Goal
Escalate from a **medium integrity token** to a **high integrity admin shell**.

---

## 🔹 Method 1: PowerShell GUI Elevation (If GUI Available)

```powershell
Start-Process powershell -Verb runAs
```

This opens a new **elevated PowerShell window**. You now have full admin rights.

---

## 🔹 Method 2: Meterpreter Post Module

```bash
run post/windows/escalate/bypassuac
```

Spawns a new elevated Meterpreter session using token duplication techniques.

---
## 🔹 Method 3: Meterpreter's Incognito load delegation token
---
- To check which tokens are available, enter the **_list_tokens -g_**. We can see that the _BUILTIN\Administrators_ token is available.
- **Use the _impersonate_token "BUILTIN\Administrators"_** command to impersonate the Administrators' token. 

- Even though you have a higher privileged token, y**ou may not have the permissions of a privileged user (this is due to the way Windows handles permissions** - it uses the Primary Token of the process and not the impersonated token to determine what the process can or cannot do).

- Ensure that you **migrate to a process with correct permissions** (the above question's answer). The safest process to pick is the **services.exe process**. First, use the _ps_ command to view processes and find the PID of the services.exe process. Migrate to this process using the command _migrate PID-OF-PROCESS_

- ==**Migrate to svchost.exe**, try multiple ones with other pids==

## ✅ Verify Elevation

```powershell
whoami /groups
whoami /priv
```

Look for:
- `High Mandatory Level`
- Enabled privileges like `SeDebugPrivilege`
