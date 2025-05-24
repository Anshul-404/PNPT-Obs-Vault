- Do not ignore anything thinking this cannot be implemented in the exam
- Treat as a REAL pentest, we need to give as much information as we can, so ENUMERATE like there is no tomorrow
- Pivot Laterally until you can pivot vertically
- **Hashes Found** - Try cracking, relaying on other targets, Passing them to login using exec tools, Dumping using secretsdump on all IPs, enumerate
- **Password Found** - Try password reuse, login using exec tools, Dumping using secretsdump on all IPs, enumerate
- **Username Found** - Try password reuse, login using exec tools, Dumping using secretsddump on all IPs, kerberoasting, enumerate

- **Server Found** - Try WDigest, kerberoasting for service account

- Both manual and tools enumeration is important with verbose and additional flags