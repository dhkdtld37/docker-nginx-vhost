# docker nginx vhost

![image](https://github.com/dhkdtld37/docker-nginx-vhost/assets/149128094/ff89e9ed-f3b4-450d-b409-25641c653a65)

![image](https://github.com/dhkdtld37/docker-nginx-vhost/assets/149128094/34b295d3-c24e-43bc-8148-62a68a677016)

## load-balancing

https://www.nginx.com/resources/glossary/load-balancing/

### Step 1

```bash
$ sudo docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
$ sudo docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

### Step 2

```bash
$ docker run -itd -p 8002:80 --name serv-a nginx
$ docker run -itd -p 8003:80 --name serv-b nginx
$ docker run -itd -p 8001:80 --name lb nginx:latest

$ sudo docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS                  NAMES
7365bdfc1e00   nginx          "/docker-entrypoint.…"   3 seconds ago        Up 3 seconds        0.0.0.0:8003->80/tcp   serv-b
8877e587dd90   nginx:latest   "/docker-entrypoint.…"   29 seconds ago       Up 29 seconds       0.0.0.0:8001->80/tcp   lb
75934d4f6218   nginx          "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8002->80/tcp   serv-a
6d29acd1e0df   nginx          "/docker-entrypoint.…"   17 hours ago         Up 17 hours         0.0.0.0:9055->80/tcp   BlogDockerHub
```

### Step 3

```bash
$ sudo docker network create aaa

$ sudo docker network inspect aaa
```

```json
[
    {
        "Name": "aaa",
        "Id": "07c667a9c932621eb50294401256c927654aa80ecfc8c492faf07aeafbb8e65f",
        "Created": "2024-02-14T04:32:45.981625726Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.20.0.0/16",
                    "Gateway": "172.20.0.1"
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
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

### Step 4

```bash
$ sudo docker network connect aaa serv-a
$ sudo docker network connect aaa serv-b
$ sudo docker network connect aaa lb
```

```bash
$ sudo docker network inspect aaa
```

```json

[
    {
        "Name": "aaa",
        "Id": "07c667a9c932621eb50294401256c927654aa80ecfc8c492faf07aeafbb8e65f",
        "Created": "2024-02-14T04:32:45.981625726Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.20.0.0/16",
                    "Gateway": "172.20.0.1"
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
            "448a705be79fd8005645cbb23dc46f2cc439f8dbc1d20778eabe5ea0c8366388": {
                "Name": "lb",
                "EndpointID": "ffc54549d51d708dd456e31129e3643f6269a03510ce1a2f814cf744a152af15",
                "MacAddress": "02:42:ac:14:00:04",
                "IPv4Address": "172.20.0.4/16",
                "IPv6Address": ""
            },
            "7365bdfc1e00201ef223d703e2e906c50baa03032aea084fa893cbf8b5be82c7": {
                "Name": "serv-b",
                "EndpointID": "636f342fa01cc31a340c908fac5dc3fe8a7f768f781d67f778b422572b3c9ae8",
                "MacAddress": "02:42:ac:14:00:03",
                "IPv4Address": "172.20.0.3/16",
                "IPv6Address": ""
            },
            "75934d4f62180a6ea872c60a0da4d2a634c1a41b67ea1c694777a88cd38650d7": {
                "Name": "serv-a",
                "EndpointID": "a250ec43ec785a2bad03ed36c9235605629a0633beebdfb4153cad82d0706839",
                "MacAddress": "02:42:ac:14:00:02",
                "IPv4Address": "172.20.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

## 
