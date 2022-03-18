---
title: docker学习
date: 2022-03-05 01:21:27
tags:
---
`docker run ubuntu:15.10 /bin/echo "Hello world"`
`docker run -i -t ubuntu:15.10 /bin/bash`
`exit`
`docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"`
`docker ps`
`docker logs 2b1b7a428627`
`docker logs amazing_cori`
`docker ps`
`docker stop amazing_cori`
`docker`
`docker stats --help`
`docker pull ubuntu`
`docker run -it ubuntu /bin/bash`
`docker ps -a`
`docker start b750bbbcfd88 `
`docker run -itd --name ubuntu-test ubuntu /bin/bash`
`docker stop <容器 ID>`
`docker restart <容器 ID>`
`docker attach 1e560fca3906 `
`docker exec -it 243c32535da7 /bin/bash`
`docker export 1e560fca3906 > ubuntu.tar`
`cat docker/ubuntu.tar | docker import - test/ubuntu:v1`
`docker import http://example.com/exampleimage.tgz example/imagerepo`
`docker rm -f 1e560fca3906`
`docker pull training/webapp`
`docker run -d -P training/webapp python app.py`
`docker ps`
`docker run -d -p 5000:5000 training/webapp python app.py`
`docker port bf08b7f2cd89`
`docker port wizardly_chandrasekhar`
`docker logs -f bf08b7f2cd89`
`docker top wizardly_chandrasekhar`
`docker inspect wizardly_chandrasekhar`
`docker stop wizardly_chandrasekhar`
`docker start wizardly_chandrasekhar`
`docker ps -l`
`docker rm wizardly_chandrasekhar`
`docker run -t -i ubuntu:15.10 /bin/bash`
`docker run -t -i ubuntu:14.04 /bin/bash`
`docker pull ubuntu:13.10`
`docker pull httpd`
`docker run httpd`
`docker rmi hello-world`
`docker run -t -i ubuntu:15.10 /bin/bash`
`docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2`
`docker run -t -i runoob/ubuntu:v2 /bin/bash`
`cat Dockerfile `
`docker build -t runoob/centos:6.7 .`
`docker run -t -i runoob/centos:6.7  /bin/bash`
`docker tag 860c279d2fec runoob/centos:dev`
`docker run -d -P training/webapp python app.py`
`docker run -d -p 5000:5000 training/webapp python app.py`
`docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py`
`docker port adoring_stonebraker 5000`
`docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py`
