- **==`Enumeration` is the most critical part of all.==** The art, the difficulty, and the goal are not to gain access to our target computer. Instead, it s ==**identifying all of the ways we could attack a target we must find.==**

# Gaining access
---
It's not hard to get access to the target system once we know how to do it. Most of the ways we can get access **we can narrow down to the following two points:**

- Functions and/or **resources that allow us to ==interact with the target and/or provide additional== information.**
- ==**Information that provides us with even more important information**== to access our target.


# Tools?
---
- Most scanning tools have a **==timeout set until they receive a response from the service.==** If this tool **==does not respond within a specific time, this service/port will be marked as closed, filtered, or unknown.==** In the last two cases, we will still be able to work with it. 
- However, if a port is marked as closed and Nmap doesn't show it to us, we will be in a bad situation.