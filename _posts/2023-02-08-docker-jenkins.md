---
layout: post
title: Jenkins Build Java App With Maven
author: "gebaocai"
header-style: text
lang: zh
tags: [Jenkins, Docker]
---

最近想用Jenkins把项目自动部署到服务器上面， 构建项目时会用到`maven`和`node`， 这几天用docker as agent的模式搭建了一下，下面是脚本:

`vim Jenkinsfile`
```
pipeline {
    agent {
        docker {
            image 'gebaocai/maven-uid1000:3.8.7'
            args '--name maven -v /home/.m2:/var/maven/.m2 -e MAVEN_CONFIG=/var/jenkins_home/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -Duser.home=/var/maven -B -DskipTests clean package'
            }
        }
    }
}
```

启动Jenkins
------

#### 创建jenkins工作目录
`mkdir -p data/jenkins/data`

`mkdir -p data/jenkins/docker-certs`

#### 编写docker-compose.yaml文件
```
jenkins-docker:
    container_name: jenkins-docker
    image: docker:dind
    restart: always
    privileged: true
    networks: 
      jenkins:
        aliases: 
          - docker
    ports:
      - 3000:3000
      - 5000:5000
      - 2376:2376
    environment:
      DOCKER_TLS_CERTDIR: /certs 
      HTTP_PROXY: 'http://192.168.0.54:7890'
      HTTPS_PROXY: 'http://192.168.0.54:7890'
      NO_PROXY: 'docker, localhost, *.test.lan'
    volumes:
      - ./data/jenkins/docker-certs:/certs/client
      - ./data/jenkins/data:/var/jenkins_home
      - /home/gebaocai/.m2:/home/.m2
    command: --storage-driver=overlay2
  jenkins-blueocean:
    container_name: jenkins-blueocean
    image: gebaocai/myjenkins-blueocean:2.375.3-1
    restart: always
    networks: 
      - jenkins
    ports:
      - 8080:8080
      - 50000:50000
    environment:
      DOCKER_HOST: 'tcp://docker:2376'
      DOCKER_CERT_PATH: /certs/client
      DOCKER_TLS_VERIFY: 1
      JAVA_OPTS: '-Dhudson.plugins.git.GitSCM.ALLOW_LOCAL_CHECKOUT=true'
      HTTP_PROXY: 'http://192.168.0.54:7890'
      HTTPS_PROXY: 'http://192.168.0.54:7890'
      NO_PROXY: 'docker, localhost, *.test.lan'
    volumes:
      - ./data/jenkins/docker-certs:/certs/client
      - ./data/jenkins/data:/var/jenkins_home
      - /home/gebaocai/.m2:/home/.m2
networks:
  jenkins:
    external: true      
```

`environment`中的`HTTP_PROXY`、`HTTPS_PROXY`、`NO_PROXY`如果没需求可以不设置
`gebaocai/myjenkins-blueocean:2.375.3-1`是通过下面脚本build出来的。

镜像脚本：
* Create Dockerfile with the following content
```
  FROM jenkins/jenkins:2.375.3
  USER root
  RUN apt-get update && apt-get install -y lsb-release
  RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg
  RUN echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
  RUN apt-get update && apt-get install -y docker-ce-cli
  USER jenkins
  RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"
```
* Build a new docker image from this Dockerfile
```
docker build -t gebaocai/myjenkins-blueocean:2.375.3-1 .
```

####启动jenkis命令

`docker-compose up -d jenkins-docker`

`docker-compose up -d jenkins-blueocean`

####查看密码并登陆

`docker logs -f jenkins-blueocean`

启动之后，会默认生成一个密码, 用做登录用.
![](/img/in-post/2023/docker-in-docker-jenkins/setup-jenkins-02-copying-initial-admin-password.png)

登录地址为：`http://127.0.0.1:8080`

配置Jenkins
----
* 登录之后，安装建议安装的插件
* 创建`pipeline`任务
![](/img/in-post/2023/docker-in-docker-jenkins/create-pipeline-job.png)
* 配置仓位地址及Jenkinsfile
![](/img/in-post/2023/docker-in-docker-jenkins/config-pipeline-job.png)