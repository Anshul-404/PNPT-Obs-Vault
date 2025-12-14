 - After fuzzing a working parameter, we now have to **fuzz the correct value that would return the `flag` content we need.**
# Custom Wordlist
---
- When it comes to fuzzing parameter values, we **may not always find a pre-made wordlist that would work for us**, as each parameter would expect a certain type of value.
- For some parameters, like usernames, we can find a pre-made wordlist for potential usernames, or we may create our own based on users that may potentially be using the website.
- In this case, we can guess that the `**id` parameter can accept a number input of some sort.** These ids can be in a custom format, **or can be sequential, like from 1-1000 or 1-1000000, and so on.**
- The simplest way is to use the following command in Bash that writes all numbers from 1-1000 to a file:
	- `for i in $(seq 1 1000); do echo $i >> ids.txt; done`

# Value Fuzzing
---
- Our command should be **fairly similar to the POST command we used to fuzz for parameters**, but our FUZZ keyword should be put where the parameter value would be, and we will use the ids.txt wordlist we just created, as follows:
	- `ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx`