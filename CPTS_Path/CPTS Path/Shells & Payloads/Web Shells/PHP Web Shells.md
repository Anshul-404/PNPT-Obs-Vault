- Hypertext Preprocessor or [PHP](https://www.php.net) is an open-source general-purpose scripting language typically used as part of a web stack that powers a website
- PHP is used by `78.6%` of all websites whose server-side programming language we know.

# wwwolf's PHP web shell
---
_WhiteWinterWolf's PHP web shell_:
- Access can be **password protected**.
- Is compatible with both **UNIX-like and Windows systems with no modification**.
- https://github.com/WhiteWinterWolf/wwwolf-php-webshell.git

# Considerations when Dealing with Web Shells
---
When utilizing web shells, consider the below potential issues that may arise during your penetration testing process:
- Web applications **sometimes automatically delete files after a pre-defined period**
- **Limited interactivity with the operating system** in terms of navigating the file system, downloading and uploading files, chaining commands together may not work (ex. `whoami && hostname`), **slowing progress**, especially when performing enumeration -Potential instability through a non-interactive web shell
- **Greater chance of leaving behind proof** that we were successful in our attack