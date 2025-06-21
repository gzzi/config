# reverse proxy

## install nginx

apt-get install nginx

## configure your webserver

open /etc/nginx/sites-enabled/default and write:

```
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

server {
    # Update this line to be your domain
    server_name <base domain>.ch;

    # These shouldn't need to be changed
    listen [::]:80 default_server ipv6only=off;
    return 301 https://$host$request_uri;
}
```

then for each endpoint of ente:
 - web app
 - public album
 - api
 - minio

you need to add:

```
server {
    server_name <sub domain>.<base domain>.ch;

    proxy_buffering off;

    location / {
        proxy_pass http://192.168.<local ip end>:<port>;
        proxy_set_header Host $host;
        proxy_redirect http:// https://;
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
}
```

Note that minio should have a extra value for upload `client_max_body_size 100M;`

You can also add home assistant to the reverse proxy the same way.

## enable ssl

https://certbot.eff.org/instructions?ws=nginx&os=pip

