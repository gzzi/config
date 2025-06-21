## Infomaniak domain name lookup

On the manager configure a dynamic dns entry for sub domaine (like ente.yourdomain.ch).

https://manager.infomaniak.com/v3/xxx/ng/admin3/domain/yyy/dynamicdns


```py
#!/usr/bin/env python3
import requests
from pathlib import Path

def main():

    public_ip_file = Path('.public_ip')

    public_ip = get_public_ip()
    print(f'My public IP is {public_ip}')

    if public_ip_file.exists() and public_ip_file.read_text() == public_ip:
        print('IP has not changed')
        return

    domain = [
        'pi',
        'ente',
        'ente-public',
        'ente-api',
        'ente-minio',
        'home',
    ]

    success = True
    for dom in domain:
        if not update_dns(public_ip, f'{dom}.<your domain>.ch'):
            success = False
            print(f'DNS update failed for {dom}')

    if not success:
        print('one DNS update failed')
        return

    public_ip_file.write_text(public_ip)
    print('DNS updated')

def get_public_ip():
    return requests.get('https://api.ipify.org').text

def update_dns(public_ip: str, domain: str):
    username = '####'
    password = '####'
    ip = public_ip
    url = f'https://infomaniak.com/nic/update?hostname={domain}&username={username}&password={password}&myip={ip}'

    r = requests.post(url)
    print(r.text)
    return r.status_code == 200

if __name__ == '__main__':
    main()
```
