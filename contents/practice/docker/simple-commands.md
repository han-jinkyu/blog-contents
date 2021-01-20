## 1\. 이미지(image) 목록

-   `docker images`

```
$ docker images
REPOSITORY                                TAG                 IMAGE ID            CREATED             SIZE
demo                                      latest              a80a386db4fd        8 minutes ago       767MB
demo                                      v2                  a80a386db4fd        8 minutes ago       767MB
demo                                      v1                  ff7d09c7f78a        47 minutes ago      765MB
nginx                                     1.18.0              0f8452db635e        13 days ago         132MB
debian                                    buster-slim         1153053544c3        13 days ago         69.2MB
```

## 2\. 이미지(images) 삭제

-   `docker rmi`
-   rmi == remove image

### ContainerID로 삭제

-   `docker rmi [container id]`

```
$ docker rmi 0f8452db635e
Untagged: nginx:1.18.0
Untagged: nginx@sha256:d550eae8cc0f12b7b62663badbbbb2fb55980e3be8741bff2e5f6008582b02b8
Deleted: sha256:0f8452db635ede139a4a84acc2e1916652cbc4b9e5abceffcfd98de344f8baf8
Deleted: sha256:a9744c55992a57bfeaba6d62c606391ecb753df662c18be6c985d0902dec921b
Deleted: sha256:34e207a3ce5daad7b137f2174b0b83fead00eb06b3b682b53f2a7db28ce97c60
Deleted: sha256:c3d7077e4496e9563bde55cfa9cdf30342d5adbffdc02a615625fb121d598d45
Deleted: sha256:a89d3177930ee2e552e52818f5db5c4a83afd6d923c46a47700e037e2b6a2ef3
```

### 이미지 이름으로 삭제

-   `docker rmi [image name]:[tag]`

```
$ docker rmi nginx:1.18.0
Untagged: nginx:1.18.0
Untagged: nginx@sha256:f88511582dffed90f5669ca763d68d07d96440f9d419648f31a3a858d67bb1b4
Deleted: sha256:0f8452db635ede139a4a84acc2e1916652cbc4b9e5abceffcfd98de344f8baf8
Deleted: sha256:a9744c55992a57bfeaba6d62c606391ecb753df662c18be6c985d0902dec921b
Deleted: sha256:34e207a3ce5daad7b137f2174b0b83fead00eb06b3b682b53f2a7db28ce97c60
Deleted: sha256:c3d7077e4496e9563bde55cfa9cdf30342d5adbffdc02a615625fb121d598d45
Deleted: sha256:a89d3177930ee2e552e52818f5db5c4a83afd6d923c46a47700e037e2b6a2ef3
```

## 3\. 새로운 컨테이너 실행

-   `docker run [options] [image]`

```
$ docker run -d -p 8888:8080 --name demo demo:latest
bbecb5c7e91d75bec3094e561bd26e3fada25351993ea72e204dcd19b749dae4
```

-   `--detach`, `-d`: 백그라운드에서 컨테이너를 실행한다.
-   `--port`, `-p`: `[외부 포트]:[컨테이너 내부 포트]`를 연결한다.
-   `--name`: 컨테이너 이름을 설정한다.

## 4\. 컨테이너 목록

-   `docker ps`

```
$ docker ps -f name=demo
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
bbecb5c7e91d        demo:latest            "/bin/sh -c 'java -j…"   18 seconds ago      Up 17 seconds       0.0.0.0:8888->8080/tcp   demo
```

-   `--filter`, `-f`: 필터를 설정한다. 자세한 필터는 [링크](https://docs.docker.com/engine/reference/commandline/ps/#filtering) 참조.
-   `--all`, `-a`: 모든 컨테이너를 표시한다. (일반적으로 정지된 컨테이너는 표시 안 됨)
-   `--quiet`. `-q`: ID만 표시한다.

## 5\. 컨테이너 정지

-   `docker stop [container id]`

```
$ docker stop bbecb5c7e91d
bbecb5c7e91d
```

## 6\. 컨테이너 삭제

-   `docker rm [container id]`

```
$ docker rm bbecb5c7e91d
bbecb5c7e91d
```

## 7\. 구동 중인 컨테이너에 커맨드 실행

-   `docker exec [options] [container] [command]`

```
$ docker exec -it 02f916f6bbc2 bash
root@02f916f6bbc2:/# 
```

-   `--interactive`, `-i`: 표춘 출력을 열어 놓는다.
-   `--tty`, `-t`: pseudo-TTY를 할당한다.

## 8\. 응용편

### 8-1. 구동 중이 아닌 컨테이너 한꺼번에 삭제하기

```
$ docker rm $(docker ps -aq -f status=exited)
```

### 8-2. None으로 표시된 이미지 한꺼번에 삭제하기

```
$ docker rmi $(docker images -aq -f dangling=true)
```

## 참고

-   [Referece documentation | Docker Documentation](https://docs.docker.com/reference/)
