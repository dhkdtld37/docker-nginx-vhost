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
