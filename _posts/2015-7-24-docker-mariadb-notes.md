---
layout: post
title: docker notes for mariadb
---

참고
* https://registry.hub.docker.com/u/library/mariadb/

아래와 같이 Dockerfile을 만든다.

```
# Dockerfile

FROM mariadb

ENV MYSQL_ROOT_PASSWORD cedsd
ENV MYSQL_USER cedsd
ENV MYSQL_PASSWORD cedsd
ENV MYSQL_DATABASE cedsd

ADD mysql-utf8.cnf /etc/mysql/conf.d/mysql-utf8.cnf
```

mysql-utf8.cnf 파일은 다음과 같다.

```
[client]
# Default is Latin1, if you need UTF-8 set this (also in server section)
default-character-set = utf8

[mysqld]
#
# * Character sets
#
# Default is Latin1, if you need UTF-8 set all this (also in client section)
#
character-set-server  = utf8
collation-server      = utf8_general_ci
character_set_server   = utf8
collation_server       = utf8_general_ci
# 아래 세 줄은 필요 없는 것 같기도 한데...
init-connect='SET NAMES utf8'
```

이제 docker build

```
$ docker build --tag cedsd-db .
```

build한 cedsd-db 이미지를 db1이라는 이름으로 실행

```
$ docker run --name db1 -d -p 3306:3306 cedsd-db
```
* -d : detached로 실행
* -p : 포트를 외부에 오픈

외부에서 container내부의 명령어를 실행. bash를 실행하면 내부로 진입하게 됨.

```
$ docker exec -it db1 /bin/bash
$ export TERM=xterm-256color; mysql -u root -p # mysql 클라이언트 실행
# mysql 접속되었으면 아래와 같이 utf8 세팅이 제대로 되었는지 확인해 보자.
> SHOW VARIABLES LIKE 'collation%';
> SHOW VARIABLES LIKE 'character_set%';
```
