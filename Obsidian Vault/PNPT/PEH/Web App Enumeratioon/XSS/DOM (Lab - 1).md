![[Pasted image 20250406061253.png]]

- **==Normal payloads like `<script> prompt(1) </script>` does not work here as this is not sent anywhere (unlike reflected). This is only stored in client browser.==**
- `<img src=x onerror=prompt(1)>`
- `<img src=x onerror="window.location.href='https://stackoverflow.com/'">`