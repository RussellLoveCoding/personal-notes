# 私服搭建



[toc]

目前遇到了个重定向死循环的问题, 

实际是 Cloudflare 在对应网站上要设置严格的 SSL 加密模式: 

1. 打开 Cloudflare 网站首页，登录
2. 打开自己的网站
3. 点击 SSL/TLS => 概述(Overview) => 您的 SSL/TLS 加密模式 选择为为完全严格.

Here is some tests run on container and host:

- test1: 在 Caddy 容器内部测试服务是否能正常访问。使用 `wget vaultwarden:80` 命令(因为没安装 curl)

- ```bash
  $ docker container ls
  CONTAINER ID   IMAGE 
  55acb0aeec93   caddy:2 
  0469c59ba0d6   vaultwarden/server:latest
  
  $ docker exec -it 55acb0aeec93 /bin/sh
  /srv # wget vaultwarden:80
  Connecting to vaultwarden:80 (172.18.0.2:80)
  saving to 'index.html'
  index.html           100% |******************************|  1241  0:00:00 ETA 
  'index.html' saved
  /srv # cat index.html
  <!doctype html><html class="theme_light"><head><meta charset="utf-8"/><meta name="viewport" content="width=1010"/><meta name="theme-color" content="#175DDC"/><title page-title>Vaultwarden Web</title><link rel="apple-touch-icon" sizes="180x180" href="images/apple-touch-icon.png"/><link rel="icon" 
  ...
  
  ```
  
- test2: 在宿主机 内测试 vaultwarden 服务的连通性，使用命令 `curl --resolve your.domain:443:127.0.0.1 https://your.domain`. `--resolve` 是一个 `curl` 的选项，用于设置一个自定义的 DNS 解析。格式为 `[host]:[port]:[ip]`。在这个命令中，`your.domain:443:127.0.0.1` 表示将域名 `your.domain` 的 `443` 端口解析到 `127.0.0.1`。

- ```bash
   $ curl --resolve your.domain:443:127.0.0.1 https://your.domain
    <!doctype html><html class="theme_light"><head><meta charset="utf-8"/><meta name="viewport" content="width=1010"/><meta name="theme-color" content="#175DDC"/><title page-title>Vaultwarden Web</title><link rel="apple-touch-icon" sizes="180x180" href="images/apple-touch-icon.png"/><link rel="icon" 
  ```

Here is my log from vaultwarden container

```Log
➜  vault dlo vaultwarden  
/--------------------------------------------------------------------\
|                        Starting Vaultwarden                        |
|                           Version 1.30.0                           |
|--------------------------------------------------------------------|
| This is an *unofficial* Bitwarden implementation, DO NOT use the   |
| official channels to report bugs/features, regardless of client.   |
| Send usage/configuration questions or feature requests to:         |
|   https://github.com/dani-garcia/vaultwarden/discussions or        |
|   https://vaultwarden.discourse.group/                             |
| Report suspected bugs/issues in the software itself at:            |
|   https://github.com/dani-garcia/vaultwarden/issues/new            |
\--------------------------------------------------------------------/

[2023-11-19 06:18:08.475][start][INFO] Rocket has launched from http://0.0.0.0:80
```


Here is my log from caddy container.

```Log
{"level":"info","ts":1700373944.7136846,"logger":"http.log.access.log0","msg":"handled request","request":{"remote_ip":"108.162.226.206","remote_port":"17006","client_ip":"108.162.226.206","proto":"HTTP/1.1","method":"GET","host":"your.domain","uri":"/","headers":{"X-Forwarded-For":["2408:8256:db97:ecc:519c:67de:5212:252d"],"Cf-Ray":["8286436178e708de-SIN"],"X-Forwarded-Proto":["https"],"Cf-Visitor":["{\"scheme\":\"https\"}"],"Sec-Fetch-Site":["none"],"Cf-Connecting-Ip":["2408:8256:db97:ecc:519c:67de:5212:252d"],"Connection":["Keep-Alive"],"Cache-Control":["max-age=0"],"Upgrade-Insecure-Requests":["1"],"User-Agent":["Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36"],"Sec-Fetch-Dest":["document"],"Sec-Ch-Ua-Mobile":["?0"],"Accept-Encoding":["gzip"],"Dnt":["1"],"Sec-Fetch-Mode":["navigate"],"Sec-Fetch-User":["?1"],"Sec-Ch-Ua":["\"Google Chrome\";v=\"119\", \"Chromium\";v=\"119\", \"Not?A_Brand\";v=\"24\""],"Sec-Ch-Ua-Platform":["\"Windows\""],"Accept-Language":["en,zh-CN;q=0.9,zh;q=0.8"],"Priority":["u=0, i"],"Cdn-Loop":["cloudflare"],"Cf-Ipcountry":["CN"],"Accept":["text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7"]}},"bytes_read":0,"user_id":"","duration":0.000044439,"size":0,"status":308,"resp_headers":{"Server":["Caddy"],"Connection":["close"],"Location":["https://your.domain/"],"Content-Type":[]}}
```

Here is my docker-compose.yml to start vaultwarden container and http reverse
proxy container caddy.

```yaml
version: '3'

services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: always
    environment:
      DOMAIN: "https://your.domain"  # Your domain; vaultwarden needs to know it's https to work properly with attachments
    volumes:
      - ./vw-data:/data


  caddy:
    image: caddy:2
    container_name: caddy
    restart: always
    ports:
      - 80:80  # Needed for the ACME HTTP-01 challenge.
      - 443:443
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - ./caddy-config:/config
      - ./caddy-data:/data
    environment:
      DOMAIN: "https://your.domain"  # Your domain.
      EMAIL: "xxx@gmail.com"                 # The email address to use for ACME registration.
      LOG_FILE: "/data/access.log"

```


Here is my Caddyfile

```Caddy
# Uncomment this in addition with the import admin_redir statement allow access to the admin interface only from local networks
{$DOMAIN}:443 {
  log {
    level INFO
    output file {$LOG_FILE} {
      roll_size 10MB
      roll_keep 10
    }
  }

  # Uncomment this if you want to get a cert via ACME (Let's Encrypt or ZeroSSL).
  tls {$EMAIL}

  encode gzip
  reverse_proxy vaultwarden:80 {
       header_up X-Real-IP {http.request.header.Cf-Connecting-Ip}
  }
}
```

Here is my container's networking seting.

```bash
➜  vault dcls                                                 
CONTAINER ID   IMAGE                       COMMAND                  CREATED       STATUS                 PORTS                                                                                         NAMES
55acb0aeec93   caddy:2                     "caddy run --config …"   2 hours ago   Up 2 hours             0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp, 443/udp, 2019/tcp   caddy
0469c59ba0d6   vaultwarden/server:latest   "/start.sh"              2 hours ago   Up 2 hours (healthy)   80/tcp, 3012/tcp                                                                              vaultwarden
➜  vault dni vault_default
[
    {
        "Name": "vault_default",
        "Id": "c2f8bccd6423d620a4bc7fd01fdf941f637103530d7cd96d273e2adc072a53f3",
        "Created": "2023-11-18T09:26:38.301699359Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "0469c59ba0d6b01269fb64c25b89b58c211cefce9573a0fd2808ece0d0b3243e": {
                "Name": "vaultwarden",
                "EndpointID": "5b7b2387d0bb30370cfb836d8fccc1bbd0eaa89e7e9354795b9b3a21ce8e7ec5",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "55acb0aeec93cb13859205c2844f0c632cf47694de6fcaf8cf9ae1fe19bed25d": {
                "Name": "caddy",
                "EndpointID": "3603fd3b0366b5663f850a3385fe090d021b1dea2f445cd71cff419a65d55d6e",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {
            "com.docker.compose.network": "default",
            "com.docker.compose.project": "vault",
            "com.docker.compose.version": "2.21.0"
        }
    }
]
```

