- There are packages for windows "**MSI**" packages -> **Windows Installed**.
- There is a **windows feature** which can be enabled to **install of these packages as elevated (Administrator)**
	- **It applies to _all_ MSI packages** — there's no restriction.
- We need to see the **value of "AlwaysInstallElevated" in registry to be '1'**

- `AlwaysInstallElevated` is a **Windows policy** that, when enabled in both the **HKLM** (machine) and **HKCU** (user) registry hives, allows users to run **Windows Installer packages (`.msi`) with elevated privileges**.

![[Pasted image 20250417150315.png]]