- `Defacing` a website means **changing its look for anyone who visits** the website.
- It is very common for hacker groups to **deface a website to claim that they had successfully hacked it**, like when hackers defaced the UK National Health Service (NHS) [back in 2018](https://www.bbc.co.uk/news/technology-43812539).
- Such attacks can carry great media echo and may significantly affect a company's investments and share prices, especially for banks and technology firms.

# Defacement Elements
---
- We can **utilize injected JavaScript code (through XSS) to make a web page look any way we like.**
- However, defacing a website is **usually used to send a simple message (i.e., we successfully hacked you),** so giving the defaced web page a beautiful look isn't really the primary target.
- **Four HTML elements are usually utilized** to change the main look of a web page:
	- **Background Color** `document.body.style.background`
	- **Background** `document.body.background`
	- **Page Title** `document.title`
	- **Page Text** `DOM.innerHTML`

# Changing Background
---
- To change a web page's background, **we can choose a certain color or use an image.** 
- We **will use a color as our background since most defacing attacks use a dark color** for the background. To do so, we can use the following payload:
	- `<script>document.body.style.background = "#141d2b"</script>`
	  
- Tip: **Here we set the background color to the default Hack The Box background color**. We can use any other hex value, **or can use a named color like `= "black"`.**
  
- Thi**s will be persistent through page refreshes and will appear for anyone** who visits the page, **==as we are utilizing a stored XSS vulnerability.==**
- Another option would be **to set an image to the background** using the following payload:
	- `<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>`

# Changing Page Title
---
- We can **change the page title from `2Do` to any title of our choosing**, using the `document.title` JavaScript property:
	- `<script>document.title = 'HackTheBox Academy'</script>`

# Changing Page Text
---
- We can **change the text of a specific HTML element/DOM using the `innerHTML`** property:
	- `document.getElementById("todo").innerHTML = "New Text"`
	  
- We can also **utilize jQuery functions for more efficiently achieving the same thing** or for changing the text of multiple elements in one line (to do so, **the `jQuery` library must have been imported within the page source**):
	- `$("#todo").html('New Text');`

- As **hacking groups usually leave a simple message on the web page and leave nothing else on it**, we will **change the entire HTML code of the main `body`, using `innerHTML`, as follows:**
	- `document.getElementsByTagName('body')[0].innerHTML = "New Text"`

- As we can see, **we can specify the `body` element with `document.getElementsByTagName('body')`, and by specifying `[0]`**, we are **selecting the first `body` element, which should change the entire text** of the web page.
- However, before sending our payload and making a permanent change, **we should prepare our HTML code separately and then use `innerHTML` to set our HTML code to the page source.**

- **Tip:** It would be **wise to try running our HTML code locally to see how it looks and to ensure that it runs as expected**, before we commit to it in our final payload.

- We will **minify the HTML code into a single line and add it to our previous XSS payload**. The final payload should be as follows:
	- `<script>document.getElementsByTagName('body')[0].innerHTML = '<center><h1 style="color: white">Cyber Security Training</h1><p style="color: white">by <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px" alt="HTB Academy"> </p></center>'</script>`