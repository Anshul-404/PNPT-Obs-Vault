- If our injected **XSS payload gets stored in the back-end database and retrieved upon visiting the page**, this means that our **XSS attack is persistent (stored) and may affect any user that visits the page.**
- This makes this type of **XSS the most critical**, as it **affects** a **much wider audience** since any user who visits the page would be a victim of this attack.

# XSS Testing Payloads
---
- We can test whether the page is vulnerable to XSS with the **following basic XSS payload:**
	- `<script>alert(window.origin)</script>`
- We use this payload as it is a **very easy-to-spot method to know when our XSS payload has been successfully executed.**
- We can confirm this further by looking at the **page source by clicking [`CTRL+U`] or right-clicking and selecting `View Page Source`, and we should see our payload in the page source:**
	- ```html
			<div></div><ul class="list-unstyled" id="todo"><ul><script>alert(window.origin)</script>
			</ul></ul>
```

- **Tip:** Many **modern web applications utilize cross-domain IFrames to handle user input,** so that **even if the web form is vulnerable to XSS**, it would **not be a vulnerability on the main web application**. 
- This is why we are **==showing the value of `window.origin` in the alert box, instead of a static value like `1`.==** 
- In this case, **the alert box would reveal the URL it is being executed on, and will confirm which form is the vulnerable one**, in **case an IFrame was being used.**

- As some modern ==**browsers may block the `alert()` JavaScript function in specific locations**,== it may be handy to know a few other basic XSS payloads to verify the existence of XSS. 
- **One such XSS payload is `<plaintext>`, which will stop rendering the HTML code that comes after it and display it as plaintext.** 
- **Another easy-to-spot payload is `<script>print()</script>`** that will **pop up the browser print dialog, which is unlikely to be blocked** by any browsers.
- **To see whether the payload is persistent and stored on the back-end, we can refresh the page** and see whether we get the alert again.