# jenkins-docker-in-docker

Dockerfile
```
FROM jenkins/jenkins:lts
 
USER root
 
COPY docker_install.sh /docker_install.sh
 
RUN chmod +x /docker_install.sh
 
RUN /docker_install.sh
```

docker_install.sh
```
#!/bin/sh
 
apt-get update && \
apt-get -y install apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    zip \
    upzip \
    software-properties-common && \
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey && \
add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
    $(lsb_release -cs) \
    stable" && \
apt-get update && \
apt-get -y install docker-ce
```

docker-compose.yml

```
version: '3'
 
services:
  jenkins:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: 'jenkins_docker'
    restart: always
    ports:
      - '9090:8080'
      - '50000:50000'
    expose:
      - '8080'
      - '50000'
    volumes:
      - './jenkins_home:/var/jenkins_home'
      - '/var/run/docker.sock:/var/run/docker.sock'
    environment:
      TZ: "Asia/Seoul"
networks:
  default:
    external:
      name: devops
```

컨테이너 생성
```
sudo docker network create devops

sudo docker-compose up -d
```

jenkins 컨테이너에 접속해서 docker 명령어 테스트
브라우저에서 http://www.example.com:9090 에 접속

```
docker exec -it jenkins_docker cat /var/jenkins_home/secrets/initialAdminPassword
```
