## XSS
---
- Many file types may **==allow us to introduce a `Stored XSS` vulnerability==** to the web application by uploading maliciously crafted versions of them.
- The most basic example is when a **web application allows us to upload `HTML` files.**
- If the **target sees a link from a website they trust,** and the **==website is vulnerable to uploading HTML documents,==** it may be possible to **==trick them into visiting the link and carry the attack on their machines.==**

- Another example of XSS attacks is **==web applications that display an image's metadata after its upload.==** 
- For such web applications, we can include an XSS payload in one of the Metadata parameters that accept raw text, ==**like the `Comment` or `Artist` parameters, as follows:**==
	- `exiftool -Comment=' "><img src=1 onerror=alert(window.origin)>' HTB.jpg`
- When the **==image's metadata is displayed, the XSS payload should be triggered==**, and the JavaScript code will be executed to carry the XSS attack.
- Furthermore, ==**if we change the image's MIME-Type to `text/html`, some web applications may show it as an HTML document**== instead of an image, in which case the ==**XSS payload would be triggered even if the metadata wasn't directly displayed.**==

- SVG Image based Payload (uploads can accept SVG as an valid image type):
- ==**`Scalable Vector Graphics (SVG)==`** images are XML-based, and **they describe 2D vector graphics**, which the browser renders into an image.
- For this reason, ==**we can modify their XML data to include an XSS payload**.== For example, we can write the following to `HTB.svg`:
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
	<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
	    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
	    <script type="text/javascript">alert(window.origin);</script>
	</svg>
```
- Once we upload the image to the web application, the XSS payload will be triggered whenever the image is displayed.

## XXE - Using SVG (XML)
---
- With **SVG images, we can also include malicious XML data to ==leak the source code of the web application==**, and other internal documents within the server. 
- The following example can be used for an SVG image that leaks the content of (`/etc/passwd`):

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
	<svg>&xxe;</svg>
```

- **Similarly, if the web application allows the ==upload of `XML` documents**==, then the **==same payload can carry the same attack when the XML data==** is displayed on the web application.
- **Access to the source code will enable us to find more vulnerabilities** to exploit within the web application through Whitebox Penetration Testing.
- For File Upload exploitation, it may allow us to `locate the upload directory, identify allowed extensions, or find the file naming scheme`, which may become handy for further exploitation.

- To use XXE to read source code in PHP web applications, we can use the following payload in our SVG image:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

- Once the SVG image is displayed, ==**we should get the base64 encoded content of `index.php`,**== which we can decode to read the source code.
- Using XML data is not unique to SVG images, as it is also utilized by many types of documents, like **`PDF`, `Word Documents`, `PowerPoint Documents`,** among many others.
- **==All of these documents include XML data within them==** to specify their format and structure.
- Suppose **a web application used a document viewer that is vulnerable to XXE** and allowed uploading any of these documents. In that case, we may also modify their XML data to include the malicious XXE elements, and we would be able to carry a blind XXE attack on the back-end web server.
- Another similar attack that is also achievable through these file **types is an SSRF attack**. We may utilize the XXE vulnerability to enumerate the internally available services or even call private APIs to perform private actions. For more about SSRF, you may refer to the [Server-side Attacks](https://academy.hackthebox.com/module/details/145) module.

# Assesment
---
- Q1. Only Allows SVG uploads:
	- ![[Pasted image 20260302010417.png]]
- Upload svg to read contents:
	- ![[Pasted image 20260302011425.png]]
- Open the profile picture and view source on browser as profile image will execute the code:
	- ![[Pasted image 20260302011455.png]]

Q2. Try to read the source code of 'upload.php' to identify the uploads directory, and use its name as the answer. (write it exactly as found in the source, without quotes)
![[Pasted image 20260302011838.png]]
![[Pasted image 20260302011849.png]]