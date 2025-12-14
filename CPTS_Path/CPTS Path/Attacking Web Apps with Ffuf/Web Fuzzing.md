# Fuzzing
---
- The term **`fuzzing` refers** to a testing technique that sends **various types of user input to a certain interface to study how it would react.** 
- If we were fuzzing for **SQL injection** vulnerabilities, we would be sending **random special characters and seeing how the server would react.** 
- If we were fuzzing for a **buffer overflow**, we would be sending **long strings and incrementing their length to see if and when the binary would break.**
- We **==usually utilize pre-defined wordlists of commonly used terms==** for each type of test for web fuzzing to see if the webserver would accept them.

# Wordlists
---
- To determine which pages exist, we should have a **wordlist containing commonly used words for web directories and pages, very similar to a `Password Dictionary Attack`**, which we will discuss later in the module.
- Some of the most commonly used wordlists can be **==found under the GitHub [SecLists](https://github.com/danielmiessler/SecLists) repository==**, which categorizes wordlists under various types of fuzzing, even including commonly used passwords, which we'll later utilize for Password Brute Forcing.
- Another **==commonly used wordlist called `directory-list-2.3`,==** and it is available in various forms and sizes.