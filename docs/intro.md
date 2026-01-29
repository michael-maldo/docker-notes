---
sidebar_position: 1
title: Intro
description: "Introduction to Docker"
tags:
- docker
- stop / start
---

# Intro
This is a Docker introduction -- this assumes Docker is already installed in your machine<br/>


## Ops

### docker version
command to test that the client and daemon (server) are running and talking to each other
```bash  title="bash"

// this shows client is talking to the server
# highlight-next-line
$ docker version

# highlight-next-line
Client:
 Version:           24.0.5
 API version:       1.43
 Go version:        go1.22.0
 Git commit:        %{shortcommit_cli}
 Built:             Sun Feb 11 23:35:47 2024
 OS/Arch:           linux/amd64
 Context:           default

# highlight-next-line
Server:
 Engine:
  Version:          24.0.5
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.22.0
  Git commit:       %{shortcommit_moby}
  Built:            Sun Feb 11 23:35:47 2024
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.23
  GitCommit:
 runc:
  Version:          1.3.0
  GitCommit:
 docker-init:
  Version:          0.19.0
  GitCommit:

// stopping docker - shows server not running
# highlight-next-line
$ sudo systemctl stop docker.service docker.socket containerd

# highlight-next-line
@:~$ docker version

# highlight-next-line
Client:
 Version:           24.0.5
 API version:       1.43
 Go version:        go1.22.0
 Git commit:        %{shortcommit_cli}
 Built:             Sun Feb 11 23:35:47 2024
 OS/Arch:           linux/amd64
 Context:           default
# highlight-next-line
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

```
### docker images
<b>Docker image</b> is like an object that contains an OS filesystem, an application, and all application dependencies.<br/>
An image gets its own <b>unique ID</b>.<br/>
When referencing images, you can refer to them using either IDs or names.
```bash title="bash"
// currently no images
# highlight-next-line
$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

// Getting images onto your Docker host is called pulling
// sample pull of ubuntu image
# highlight-next-line
$ docker pull ubuntu:latest
latest: Pulling from library/ubuntu
20043066d3d5: Pull complete
Digest: sha256:c35e29c9450151419d9448b0fd75374fec4fff364a27f176fb458d472dfc9e54
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest

// docker after pull - showing ubuntu image
# highlight-next-line
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
ubuntu       latest    c3a134f2ace4   2 months ago   78.1MB

```

### docker containers
<b>docker run</b> command to launch a container from the <b>ubuntu</b> image<br/>
The <b>-it</b> flags tell Docker<br/>
to make the container interactive<br/>
and to attach the current shell to the container’s terminal<br/>

```bash title="bash"
// issuing some commands inside the running ubuntu image container
# highlight-next-line
$ docker run -it ubuntu:latest /bin/bash
root@a1bb606e08be:/#
root@a1bb606e08be:/#
root@a1bb606e08be:/# hostname
a1bb606e08be
root@a1bb606e08be:/# uname -a
Linux a1bb606e08be 6.14.5-100.fc40.x86_64 #1 SMP PREEMPT_DYNAMIC Fri May  2 14:22:13 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
root@a1bb606e08be:/# date
Wed Dec 31 13:57:37 UTC 2025
root@a1bb606e08be:/# whoami
root
root@a1bb606e08be:/# ps
    PID TTY          TIME CMD
      1 pts/0    00:00:00 bash
     13 pts/0    00:00:00 ps
root@a1bb606e08be:/#

```

### Exit container without terminating
Press <b>Ctrl-PQ</b> to exit the container without terminating it. <br/>
Exit from the container without killing it. <br/>
<b>docker ps</b> command - to see all running containers <br/>

```bash title="bash"

// docker ps showing active running containers
// below showing the ubuntu image container running - 26 minutes
$ docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED          STATUS          PORTS     NAMES
# highlight-next-line
a1bb606e08be   ubuntu:latest   "/bin/bash"   26 minutes ago   Up 26 minutes             youthful_vaughan

```

### Connecting to running containers

```bash title="bash"
// using CONTAINER NAME
$ docker exec -it youthful_vaughan bash
root@a1bb606e08be:/#
root@a1bb606e08be:/# read escape sequence
$

// using CONTAINER ID
$ docker exec -it a1bb606e08be bash
root@a1bb606e08be:/#
root@a1bb606e08be:/#
root@a1bb606e08be:/# read escape sequence
$
$

// using shortcut - first few characters unique part of the CONTAINER ID
// as long as it is unique
$ docker exec -it a1 bash
root@a1bb606e08be:/#
root@a1bb606e08be:/# read escape sequence
$
$

```

### Stopping / Killing running containers
Stop the container and kill it <br/>
using the <b>docker stop CONTAINER_ID | NAME</b> -- to stop a running container<br/>
and <b>docker rm CONTAINER_ID | NAME</b> command to remove a container -- only after it has stopped<br/>
substitute the names/IDs of the containers.<br/>

After container is stopped<br/>
Remove Images you want deleted<br/>
Use <b>docker rmi REPOSITORY[:TAG] | IMAGE_ID</b><br/>


```bash title="bash"
// check running containers
$ docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED             STATUS             PORTS     NAMES
a1bb606e08be   ubuntu:latest   "/bin/bash"   About an hour ago   Up About an hour             youthful_vaughan
$

// unsuccessful removal of still running container
$ docker rm a1bb606e08be
Error response from daemon: You cannot remove a running container a1bb606e08bedc6a05b8696f08d7790763baa60426d7220359d5115bb860af45. Stop the container before attempting removal or force remove
$
$

// stoping a running container
$ docker stop a1bb606e08be
a1bb606e08be
$

// docker ps  -- show container removed from the list
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

// docker ps -a -- still showing container
$ docker ps -a
CONTAINER ID   IMAGE           COMMAND       CREATED             STATUS                        PORTS     NAMES
a1bb606e08be   ubuntu:latest   "/bin/bash"   About an hour ago   Exited (137) 22 seconds ago             youthful_vaughan
$

// safe - successful removal of a stopped container
$ docker rm a1bb606e08be
a1bb606e08be
$
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
ubuntu       latest    c3a134f2ace4   2 months ago   78.1MB
$
$

// image can be removed only after the container is stopped / removed
$ docker rmi ubuntu
Untagged: ubuntu:latest
Untagged: ubuntu@sha256:c35e29c9450151419d9448b0fd75374fec4fff364a27f176fb458d472dfc9e54
Deleted: sha256:c3a134f2ace4f6d480733efcfef27c60ea8ed48be1cd36f2c17ec0729775b2c8
Deleted: sha256:e8bce0aabd687e9ee90e0bada33884f40b277196f72aac9934357472863a80ae
$


```

## Dev

### clone an app from a Git repo

```bash title="bash"

$ git clone https://github.com/nigelpoulton/psweb.git
Cloning into 'psweb'...
remote: Enumerating objects: 63, done.
remote: Counting objects: 100% (34/34), done.
remote: Compressing objects: 100% (22/22), done.
remote: Total 63 (delta 13), reused 25 (delta 9), pack-reused 29
Receiving objects: 100% (63/63), 13.29 KiB | 4.43 MiB/s, done.
Resolving deltas: 100% (21/21), done.

```


### inspect its Dockerfile

```bash title="bash"

$ cd psweb
$ ls -l
total 40
-rw-r--r--@ 1 ubuntu ubuntu
-rw-r--r--@ 1 ubuntu ubuntu
-rw-r--r--@ 1 ubuntu ubuntu
-rw-r--r-- 1 ubuntu ubuntu
-rw-r--r--@ 1 ubuntu ubuntu
drwxr-xr-x 4 ubuntu ubuntu
drwxr-xr-x 3 ubuntu ubuntu
338 24 Apr 19:29 Dockerfile
396 24 Apr 19:32 README.md
341 24 Apr 19:29 app.js
216 24 Apr 19:29 circle.yml
377 24 Apr 19:36 package.json
128 24 Apr 19:29 test
96 24 Apr 19:29 views

$ cat Dockerfile
FROM alpine
LABEL maintainer="nigelpoulton@hotmail.com"
RUN apk add --update nodejs nodejs-npm
COPY . /src
WORKDIR /src
RUN npm install
EXPOSE 8080
ENTRYPOINT ["node", "./app.js"]



```

### containerize and run app container.

```

$ docker build -t test:latest .
[+] Building 36.2s (11/11) FINISHED
=> [internal] load .dockerignore
=> => transferring context: 2B
=> [internal] load build definition from Dockerfile
<Snip>
=> => naming to docker.io/library/test:latest
=> => unpacking to docker.io/library/test:latest
0.0s
0.0s
0.0s
0.0s
0.7s

$ docker images
REPO
TAG
test
latest
IMAGE ID
1ede254e072b
CREATED
7 seconds ago
SIZE
154MB

$ docker run -d \
--name web1 \
--publish 8080:8080 \
test:latest

```
### retagging images
```sh title="bash"
docker images
REPOSITORY                        TAG       IMAGE ID       SIZE
gnostex-backend-api                latest    def456
gnostex-backend-api                abc123    abc123

docker tag abc123 gnostex-backend-api:latest

docker images
REPOSITORY                        TAG       IMAGE ID       SIZE
gnostex-backend-api                latest    abc123
gnostex-backend-api                def456    def456
gnostex-backend-api                abc123    abc123

// Remove old latest tag reference
docker rmi gnostex-backend-api:latest

// Tags are just labels, not copies of images.
// You can move tags freely — this is how you can roll back latest to a previous build.
// Useful for manual rollback on VPS:

docker stop gnostex-backend-api
docker rm gnostex-backend-api
docker tag abc123 gnostex-backend-api:latest
docker run -d --name gnostex-backend-api -p 8080:8080 gnostex-backend-api:latest
```

## Local
This example shows a basic local setup using **Spring Boot**, **PostgreSQL**, and **Docker**.<br/>
The Spring Boot application runs inside a Docker container and connects to a local PostgreSQL database.

### set-up

#### .env
```ini title="[root project directory]/.env"
DB_USERNAME=gnostex
DB_PASSWORD=gnostex
DB_URL=jdbc:postgresql://host.docker.internal:5432/gnostexdb
SPRING_PROFILES_ACTIVE=dev
```
#### Dockerfile
```docker title="[root project directory]/Dockerfile"
# =========================
# Build stage
# =========================
FROM maven:3.9.9-eclipse-temurin-21 AS build
WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn -B -Dmaven.test.skip=true clean package



# =========================
# Runtime stage
# =========================
FROM eclipse-temurin:21-jre
WORKDIR /app

# add curl
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

# Copy jar from build stage
COPY --from=build /app/target/gnostex-backend-api-0.0.1-SNAPSHOT.jar app.jar

# Copy source and target for exploration
# Comment out in production
#COPY --from=build /app/src ./src
#COPY --from=build /app/target ./target
#COPY --from=build /app/pom.xml ./pom.xml


# App port
EXPOSE 8080

# Optional JVM tuning (safe defaults)
ENV JAVA_OPTS=""

# health check
HEALTHCHECK --interval=30s --timeout=5s --start-period=20s \
  CMD curl -f http://localhost:8080/actuator/health || exit 1

# Run Spring Boot
ENTRYPOINT ["sh","-c","java $JAVA_OPTS -jar /app/app.jar"]
```

### building
run the docker command to build the image.<br/>
use the -t option to name en image and version label.<br/>
point the command where [path] to the Dockerfile location<br/>
```bash title="[root project directory]/ "
// -t tag -- this will set the name of the image / version label
// [.] -- the directory contain the Dockerfile

docker build -t gnostex-api:local .
```

Use ` docker images ` to list all Docker images available on your local machine. <br/>
Started with empty list, build, then we have ` myapp:local ` listed in the local machine.

``` bash title="sample output"
$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE


$ docker build -t myapp:local .
Sending build context to Docker daemon  140.8kB
Step 1/14 : FROM maven:3.9.9-eclipse-temurin-21 AS build
3.9.9-eclipse-temurin-21: Pulling from library/maven
d9d352c11bbd: Pull complete
380ab39b4f9f: Pull complete
.
.
.
.
Successfully tagged myapp:local


$ docker images
REPOSITORY        TAG                        IMAGE ID       CREATED              SIZE
myapp             local                      ec2be4ef4307   32 seconds ago       347MB
<none>            <none>                     d1f20d771af3   About a minute ago   777MB
eclipse-temurin   21-jre                     2e463b829a56   7 weeks ago          287MB
maven             3.9.9-eclipse-temurin-21   4efe8769cc32   16 months ago        530MB

```
### running


```bash

$ docker run \
--name myapp-backend \
--env-file .env \
-p 8080:8080 \
--add-host=host.docker.internal:host-gateway  \
myapp:local


  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.5.9)

2026-01-07T07:32:56.038Z  INFO 7 --- [springboot-postgresql-docker-githubactions] [           main] PostgresqlDockerGithubactionsApplication : Starting SpringbootPostgresqlDockerGithubactionsApplication v0.0.1-SNAPSHOT using Java 21.0.9 with PID 7 (/app/app.jar started by root in /app)
2026-01-07T07:32:56.041Z  INFO 7 --- [springboot-postgresql-docker-githubactions] [           main] PostgresqlDockerGithubactionsApplication : The following 1 profile is active: "dev"
2026-01-07T07:32:57.028Z  INFO 7 --- [springboot-postgresql-docker-githubactions] [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
2026-01-07T07:32:57.053Z  INFO 7 --- [springboot-postgresql-docker-githubactions] [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 15 ms. Found 0 JPA reposito
.
.
.
.
2026-01-07T07:33:25.226Z  INFO 7 --- [springboot-postgresql-docker-githubactions] [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2026-01-07T07:33:25.227Z  INFO 7 --- [springboot-postgresql-docker-githubactions] [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2026-01-07T07:33:25.228Z  INFO 7 --- [springboot-postgresql-docker-githubactions] [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 0 ms
```

```bash title=" # from another terminal"
$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED              STATUS                        PORTS                                       NAMES
818a93282e59   myapp:local   "sh -c 'java $JAVA_O…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   myapp-backend

$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS                   PORTS                                       NAMES
818a93282e59   myapp:local   "sh -c 'java $JAVA_O…"   2 minutes ago   Up 2 minutes (healthy)   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   myapp-backend
```

### interrogating

#### using a browser
just point your browser to the local URL
```

http://localhost:8080/actuator/health

```

#### using curl
- curl with jq -- a commandline JSON processor, used here to prettify JSON
```bash
$ curl http://localhost:8080/actuator/health | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   342    0   342    0     0  61301      0 --:--:-- --:--:-- --:--:-- 68400
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "PostgreSQL",
        "validationQuery": "isValid()"
      }
    },
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 603861286912,
        "free": 464497238016,
        "threshold": 10485760,
        "path": "/app/.",
        "exists": true
      }
    },
    "ping": {
      "status": "UP"
    },
    "ssl": {
      "status": "UP",
      "details": {
        "validChains": [],
        "invalidChains": []
      }
    }
  }
}


```

#### connecting to the `  myapp-backend  ` running container
issuing commands inside the shell.
- use ` docker ps ` to check images that are running
- once you have identified the container you wanted to connect to<br/>
  issue ` docker -exec -it [cotaniner id]|[name] sh`<br/>
  ` -exec ` used to issued command to a running cotainer<br/>
  ` -it ` interactive with tty allocated<br/>
```

$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                    PORTS                                       NAMES
818a93282e59   myapp:local   "sh -c 'java $JAVA_O…"   31 minutes ago   Up 30 minutes (healthy)   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   myapp-backend


$ docker exec -it myapp-backend sh
# hostname
818a93282e59
# uname -a
Linux 818a93282e59 6.14.5-100.fc40.x86_64 #1 SMP PREEMPT_DYNAMIC Fri May  2 14:22:13 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
# date
Wed Jan  7 07:57:15 AM UTC 2026
# pwd
/app
# ls
app.jar
# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 07:32 ?        00:00:00 sh -c java $JAVA_OPTS -jar /app/app.jar
root           7       1  1 07:32 ?        00:00:29 java -jar /app/app.jar
root         405       0  0 07:56 pts/0    00:00:00 sh
root         411       0  0 07:56 pts/1    00:00:00 sh
root         417       0  0 07:56 pts/2    00:00:00 sh
root         430       0  0 07:57 pts/3    00:00:00 sh
root         449     430  0 07:57 pts/3    00:00:00 ps -ef
#


```

### cleaning up
resetting and clearing all containers / images


```bash
// checking available images locally
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myapp        local     ec2be4ef4307   58 minutes ago   347MB
$

// trying to remove an image that is currently being used by a container
$ docker rmi myapp:local
Error response from daemon: conflict: unable to remove repository reference "myapp:local" (must force) - container 818a93282e59 is using its referenced image ec2be4ef4307
$
$

// using docker ps command check running container
$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                    PORTS                                       NAMES
818a93282e59   myapp:local   "sh -c 'java $JAVA_O…"   47 minutes ago   Up 47 minutes (healthy)   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   myapp-backend

// docker ps -a give a list of all containers, running or exited (but still being referenced)
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                    PORTS                                       NAMES
818a93282e59   myapp:local   "sh -c 'java $JAVA_O…"   48 minutes ago   Up 48 minutes (healthy)   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   myapp-backend
$
$

// stopping ` myapp-backend ` container
$ docker stop myapp-backend
myapp-backend

// ` docker ps ` now showing NO RUNNING CONTAINER after ` myapp-backend ` was stopped
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

// attempt to remove the image ` myapp:local ` - failed since image is still being refrenced
$ docker rmi myapp:local
Error response from daemon: conflict: unable to remove repository reference "myapp:local" (must force) - container 818a93282e59 is using its referenced image ec2be4ef4307
$
$

// ` docker ps -a ` show image ` myapp:local ` though stopped still is being referenced inside the ` myapp-backend `
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                        PORTS     NAMES
818a93282e59   myapp:local   "sh -c 'java $JAVA_O…"   48 minutes ago   Exited (137) 23 seconds ago             myapp-backend
$
$

// removing the container myapp-backend -- only possible if container is stopped
$ docker rm myapp-backend
myapp-backend

// `docker ps -a` now shows no container refrenced
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

// image can be successfully removed when no container is refrencing the image
$ docker rmi myapp:local
Untagged: myapp:local
Deleted: sha256:ec2be4ef4307d65aceb931586183105f1ed9537448db6b18558383af9a422663
Deleted: sha256:89452ae547988b560a19473531cda7e55b04d9d34aa82a64780bce22f561c314
Deleted: sha256:f4175ef030810f3647908804414a9155668a9ebb74103c841886d087c999340f
Deleted: sha256:086485045b5f8766321d6002b2dba8b6ee5ddec6770fdcf23e684dfd3166e431
Deleted: sha256:232fab3b6c7e9e6d7c3f7375bb3143ad5678a901d259228d104ab2a0c6d41c23
Deleted: sha256:341305565a59d36f93f5edd507e3ea5f58eb393ea190c0baced61fd3a8ba1398
Deleted: sha256:9df867464039faa8eebe54a8cc77e2f341613ca37ad601008c4b4216eee6c701
Deleted: sha256:08c45221a1cf843bb6fec6b69f6573b8c05737728730fba0916fae3d4f5c67d4
Deleted: sha256:43f7baff39c3f938d19119029caad63a0fabc466c43fa9472b7894b6153942a2
Deleted: sha256:ae0fbfcdfa70a5b6f6b31867ad9a22cbf4a4dfb6ce28077f31eb1ea88680ca60
Deleted: sha256:2e463b829a56f5f3a69c181b83f2cbb2a56b90df555c213716d5b3811f956be1
Deleted: sha256:cb77dbe80adb5684fcee23d66bc899144981bc872391747f5a28e536a50d4544
Deleted: sha256:f796d3d2f8f2f8ef58906298c82b320f4f3139c377dd76c60457ba11e17fd854
Deleted: sha256:e30ce81b4172b4bbe85d45ab2863986ac77a511d8f5b2ee52c27347617865f6a
Deleted: sha256:5ff83b5d3260c90aedc2781a761c6c1acbb82458be88f092b15788eafcb35db4
Deleted: sha256:e8bce0aabd687e9ee90e0bada33884f40b277196f72aac9934357472863a80ae
$

// below commands shows local docker
// no images
// no container process running
// no referenced docker images
$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
$
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$
$
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$
$

```

## Docker Compose
### tie up images springboot-app(s) postgresql

## new commands
```
docker events  --- see docker restarts, kills, exits in real time. <br/>
docker logs -f gnostex-backend -- equivalent of tail -f logs <br/>
docker run -d --rm  --name gnostex-backend-api --add-host=host.docker.internal:host-gateway  -p 8080:8080  -e SPRING_PROFILES_ACTIVE=dev  -e DB_URL=jdbc:postgresql://host.docker.internal:5432/gnostexdb  -e DB_USERNAME=gnostex  -e DB_PASSWORD=gnostex  gnostex-backend-api <br/>
--add-host=host.docker.internal:host-gateway -- Map host.docker.internal to the Linux host<br/>
try running postgresql also as a container <br/>
docker build -t gnostex-backend-api . -- docker build <br/>
docker run -it --rm  --entrypoint sh  gnostex-backend-api  -- for exploring the image<br/>
curl -f http://localhost:8080/actuator/health

{"status":"UP","components":{"db":{"status":"UP","details":{"database":"PostgreSQL","validationQuery":"isValid()"}},"diskSpace":{"status":"UP","details":{"total":206900281344,"free":195738656768,"threshold":10485760,"path":"/app/.","exists":true}},"ping":{"status":"UP"},"ssl":{"status":"UP","details":{"validChains":[],"invalidChains":[]}}}}

docker inspect
docker inspect --format='{{.State.Health.Status}}' gnostex-backend-api
```





