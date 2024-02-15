## v 0.1.0 / Docker

- serv-a, serv-b, lb 에 대해서
- [x] A. 각각 Dockerfile 을 생성
- [x] B. 빌드
- [x] C. RUN
- [x] D. 네트워크 생성하여 serv-a, serv-b, lb 를 묶고
- [x] E. 잘 동작하는지 확인 및 README.md 에 설명 작성

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
vi config/default.conf

upstream serv {
    server serv-a:80;
    server serv-b:80;
}
server {
    listen 80;

    location /
    {
        proxy_pass http://serv;
    }
}
```

### Step 4

```bash
$ sudo docker exec -it lb bash 에 들어가서.
root@2127fcb00f0d:/# cd etc/nginx/conf.d 폴더 안에 있는 default.conf 를 step3에 만든 default.conf로 바꾸기.
$ sudo docker cp config/default.conf lb:/etc/nginx/conf.d/
```

### Step 5

```bash
$ mkdir lb serv-a serv-b //폴더 3개 만들기
$ mv config lb // config폴더를 lb폴더로 옮기기

$ tree
.
├── README.md
├── lb
│   └── config
│       └── default.conf
├── serv-a
└── serv-b
```

### Step 6

```bash
$ vi serv-a/index.html  <h1>A</h1>
$ cp serv-a/index.html serv-b
$ vi serv-b/index.html  <h1>A</h1>

각각 파일 만들어서 컨테이너 /usr/share/nginx/html/ 밑에 각각 cp하기
$ sudo docker cp serv-a/index.html serv-a:/usr/share/nginx/html/
$ sudo docker cp serv-b/index.html serv-b:/usr/share/nginx/html/
```

### Step 7

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

### Step 8

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

### Step 9

```bash
각 serv-a,b 컨테이너에 들어가서 ping, telnet 설치
$ sudo docker exec -it serv-b bash
$ sudo docker exec -it serv-a bash

root@28bdc543cf50:/# apt update
root@28bdc543cf50:/# apt install iputils-ping
root@28bdc543cf50:/# apt install telnet

serv-a에 다시 접속해서
root@28bdc543cf50:/# ping 172.18.0.2(serv-b IP)  또는 ping serv-b

PING serv-b (172.18.0.3) 56(84) bytes of data.
64 bytes from serv-b.ablb (172.18.0.3): icmp_seq=1 ttl=64 time=0.767 ms
64 bytes from serv-b.ablb (172.18.0.3): icmp_seq=2 ttl=64 time=0.095 ms
64 bytes from serv-b.ablb (172.18.0.3): icmp_seq=3 ttl=64 time=0.070 ms
64 bytes from serv-b.ablb (172.18.0.3): icmp_seq=4 ttl=64 time=0.106 ms

root@28bdc543cf50:/# telnet serv-b 80  // 8002,8003포트가 아닌 docker 내부 포트 이용 network설정을 하면 8002,8003포트가 필요가 없어진다.
Trying 172.18.0.3...
Connected to serv-b.
Escape character is '^]'.
```

### Step 10

```bash
serv-a 와 serv-b의 포트번호인 8002,8003 을 없애고, 8001 포트인 lb만을 통해서 접속하는 실습
$ sudo docker commit serv-a memento12/serv-a
$ sudo docker commit serv-b memento12/serv-b

$ docker rm serv-a
$ docker rm serv-b

$ docker run --name serv-a -d memento12/serv-a  // -p옵션 없이 run 하기
$ docker run --name serv-b -d memento12/serv-b  // -p옵션 없이 run 하기

$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS          PORTS                                   NAMES
3cbb0d8cb116   memento12/serv-a   "/docker-entrypoint.…"   4 minutes ago   Up 4 minutes    80/tcp                                  serv-a
3522b218f63e   memento12/serv-b   "/docker-entrypoint.…"   7 minutes ago   Up 7 minutes    80/tcp                                  serv-b
2127fcb00f0d   nginx:latest       "/docker-entrypoint.…"   4 hours ago     Up 55 minutes   0.0.0.0:8001->80/tcp, :::8001->80/tcp   lb

$ docker network connect ablb serv-a
$ docker network connect ablb serv-b

$ sudo docker network inspect ablb // 컨테이너 확인
```

### Step 11

```bash
도커 파일 생성 
$ vi lb/Dockerfile
From nginx
COPY config/default.conf /etc/nginx/conf.d/

$ vi serv-a/Dockerfile 
FROM nginx
COPY index.html /usr/share/nginx/html

$ vi serv-b/Dockerfile 
FROM nginx
COPY index.html /usr/share/nginx/html

build
$ sudo docker build -t serv-b:\n0.1.0 .
$ sudo docker build -t serv-b:0.1.0 .
$ sudo docker build -t lb:0.1.0 .

run
$ sudo docker run -d --name serv-a serv-a:0.1.0
$ sudo docker run -d --name serv-b serv-b:0.1.0
$ sudo docker run -d --name lb -p 8001:80 lb:0.1.0

network
$ docker network create dockerfileNW
$ sudo docker network connect dockerfileNW serv-a
$ sudo docker network connect dockerfileNW serv-b
$ sudo docker network connect dockerfileNW lb
```
