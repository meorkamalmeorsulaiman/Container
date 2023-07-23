# Building an Image

- A directory called **build context** which contains all the application source code, dependecies
- It is a common place where you place a Dockerfile

# Dockerfile

- FIle that describes an app and let Docker know how to convert it into an image
- Example of Dockerfile

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ cat Dockerfile 
# Test web-app to use with Pluralsight courses and Docker Deep Dive book
FROM alpine

LABEL maintainer="nigelpoulton@hotmail.com"

# Install Node and NPM
RUN apk add --update nodejs npm curl

# Copy app to /src
COPY . /src

WORKDIR /src

# Install dependencies
RUN  npm install

EXPOSE 8080

ENTRYPOINT ["node", "./app.js"]
```

- It will create 5 layers, you can see with inspecting and during the build:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ sudo docker build -t ddd-book:ch8.1 .
[+] Building 13.0s (10/10) FINISHED                                                                                                                            docker:default
 => [internal] load build definition from Dockerfile                                                                                                                     0.0s
 => => transferring dockerfile: 363B                                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/alpine:latest                                                                                                         0.0s
 => [1/5] FROM docker.io/library/alpine                                                                                                                                  0.0s
 => [internal] load build context                                                                                                                                        0.0s
 => => transferring context: 8.71kB                                                                                                                                      0.0s
 => CACHED [2/5] RUN apk add --update nodejs npm curl                                                                                                                    0.0s
 => [3/5] COPY . /src                                                                                                                                                    0.0s
 => [4/5] WORKDIR /src                                                                                                                                                   0.0s
 => [5/5] RUN  npm install                                                                                                                                              12.7s
 => exporting to image                                                                                                                                                   0.3s
 => => exporting layers                                                                                                                                                  0.3s
 => => writing image sha256:fa76995d62c307eaeb353320b379f2f670b9771e6bf4d5578669b4a0f196e4b4                                                                             0.0s
 => => naming to docker.io/library/ddd-book:ch8.1                   
 kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ sudo docker inspect ddd-book:ch8.1
[
    {
        "Id": "sha256:fa76995d62c307eaeb353320b379f2f670b9771e6bf4d5578669b4a0f196e4b4",
        "RepoTags": [
            "ddd-book:ch8.1"
        ],
        "RepoDigests": [],
        "Parent": "",
        "Comment": "buildkit.dockerfile.v0",
        "Created": "2023-07-23T20:58:57.38049644+08:00",
        "Container": "",
        "ContainerConfig": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": null,
            "Cmd": null,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "DockerVersion": "",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "8080/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": null,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "/src",
            "Entrypoint": [
                "node",
                "./app.js"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "nigelpoulton@hotmail.com"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 98125153,
        "VirtualSize": 98125153,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/x81ih62prohyp3b183wosdygk/diff:/var/lib/docker/overlay2/f0lbvv31c6cd9wa5cz0kdgz2r/diff:/var/lib/docker/overlay2/rbv2igqdssm0hyw3uwz8h31w7/diff:/var/lib/docker/overlay2/38fe6ac3b72c5892c885b7e6d42524e444a3d85693c92001b314a5bd73f4596d/diff",
                "MergedDir": "/var/lib/docker/overlay2/l2x6sh7tgs0g4gxzpcnam9u7b/merged",
                "UpperDir": "/var/lib/docker/overlay2/l2x6sh7tgs0g4gxzpcnam9u7b/diff",
                "WorkDir": "/var/lib/docker/overlay2/l2x6sh7tgs0g4gxzpcnam9u7b/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:78a822fe2a2d2c84f3de4a403188c45f623017d6a4521d23047c9fbb0801794c",
                "sha256:c8db13fafc82dc02ead81465de4b19287a8301d1835e75bbfa2b08021d47e91c",
                "sha256:87bf7968edfc0eedbec51d02dcdbcd05b33b1fde9dea8e36399cc906d7b91974",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:3c2d2fcc2391da1b7f3abe22325d298bd336641c62f2abd47d255d170598c139"
            ]
        },
        "Metadata": {
            "LastTagTime": "2023-07-23T20:58:57.641539459+08:00"
        }
    }
]
```
- Loading up into docker images

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: kamalsulaiman97
Password: 
WARNING! Your password will be stored unencrypted in /home/kamal/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ dockerimages
dockerimages: command not found
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ docker images
REPOSITORY              TAG       IMAGE ID       CREATED          SIZE
ddd-book                ch8.1     fa76995d62c3   14 minutes ago   98.1MB
test                    latest    096d13607584   29 hours ago     101MB
ubuntu                  latest    5a81c4b8502e   3 weeks ago      77.8MB
alpine                  latest    c1aabb73d233   5 weeks ago      7.33MB
nigelpoulton/ddd-book   web0.1    bf819e4fb31b   2 months ago     95.4MB
hello-world             latest    9c7a54a9a43c   2 months ago     13.3kB
prom/snmp-generator     v0.19.0   f9953a416350   2 years ago      926MB
prom/snmp-exporter      v0.19.0   d2a8ce729b15   2 years ago      17.8MB
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ docker tag ddd-book:ch8.1 kamalsulaiman97/ddd-book:ch8.1
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ docker images
REPOSITORY                 TAG       IMAGE ID       CREATED          SIZE
kamalsulaiman97/ddd-book   ch8.1     fa76995d62c3   14 minutes ago   98.1MB
ddd-book                   ch8.1     fa76995d62c3   14 minutes ago   98.1MB
test                       latest    096d13607584   29 hours ago     101MB
ubuntu                     latest    5a81c4b8502e   3 weeks ago      77.8MB
alpine                     latest    c1aabb73d233   5 weeks ago      7.33MB
nigelpoulton/ddd-book      web0.1    bf819e4fb31b   2 months ago     95.4MB
hello-world                latest    9c7a54a9a43c   2 months ago     13.3kB
prom/snmp-generator        v0.19.0   f9953a416350   2 years ago      926MB
prom/snmp-exporter         v0.19.0   d2a8ce729b15   2 years ago      17.8MB
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ docker push kamalsulaiman97/ddd-book:ch8.1
The push refers to repository [docker.io/kamalsulaiman97/ddd-book]
3c2d2fcc2391: Pushed 
5f70bf18a086: Mounted from nigelpoulton/ddd-book 
87bf7968edfc: Pushed 
c8db13fafc82: Pushed 
78a822fe2a2d: Mounted from library/alpine 
ch8.1: digest: sha256:ee3dd9aefdc6273d4ab4382e865baf8c1e39897502d28db4429cd4e2aeec7fa4 size: 1366
kamal@TS-Kamal:~/docker/lab/git/ddd-book/web-app$ 
```

- Command **docker build** parses the Dockerfile one-line-at-a-time starting from the top.
- Comment **#*
- Non-comment lines are **Instrucstions**
- Formate of instructoins:

```
<INSTRUCTION< <arguments>
```

- Instruction not case-sensitive
- Some instruction create layer and other is metadata
- Example of instructions that will create new layers:

```
FROM
RUN
COPY
```

- Metadata example:

```EXPOSE, WORKDIR, ENV & ENTRYPOINT```

- If instruction that adds content such as files/progesm - it will create a new layer.
- If instruct on how to build image and run the container - that's metadat
- Example:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ docker history ddd-book:ch8.1
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
fa76995d62c3   25 minutes ago   ENTRYPOINT ["node" "./app.js"]                  0B        buildkit.dockerfile.v0
<missing>      25 minutes ago   EXPOSE map[8080/tcp:{}]                         0B        buildkit.dockerfile.v0
<missing>      25 minutes ago   RUN /bin/sh -c npm install # buildkit           26.5MB    buildkit.dockerfile.v0
<missing>      25 minutes ago   WORKDIR /src                                    0B        buildkit.dockerfile.v0
<missing>      25 minutes ago   COPY . /src # buildkit                          8.44kB    buildkit.dockerfile.v0
<missing>      29 hours ago     RUN /bin/sh -c apk add --update nodejs npm c…   64.3MB    buildkit.dockerfile.v0
<missing>      29 hours ago     LABEL maintainer=nigelpoulton@hotmail.com       0B        buildkit.dockerfile.v0
<missing>      5 weeks ago      /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B        
<missing>      5 weeks ago      /bin/sh -c #(nop) ADD file:1da756d12551a0e3e…   7.33MB  
```

- Those lines that got size added, means it create new layer

# Multi-stage Builds

- Example:

```
FROM golang:1.20-alpine AS base
WORKDIR /src
COPY go.mod go.sum .
RUN go mod download
COPY . .

FROM base AS build-client
RUN go build -o /bin/client ./cmd/client

FROM base AS build-server
RUN go build -o /bin/server ./cmd/server

FROM scratch AS prod
COPY --from=build-client /bin/client /bin/
COPY --from=build-server /bin/server /bin/
ENTRYPOINT [ "/bin/server" ]
```

- Contain 4 stages: base, build-client, build-server & prod (these are friendly name)
- See the 2nd and 3rd stage, it doesn't pull a new image.
- It uses the **FROM base AS build-client** instruction, so that it uses image created by the **base** stage
- The final stage **prod** pull minimal scratch image
- It uses **COPY --from** to copy the complied client app from **build-client** stage and **build-server**
- Example:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ docker build -t multi:stage .
[+] Building 37.0s (15/15) FINISHED                                                                                                                            docker:default
 => [internal] load build definition from Dockerfile                                                                                                                     0.0s
 => => transferring dockerfile: 407B                                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/golang:1.20-alpine                                                                                                    3.8s
 => [auth] library/golang:pull token for registry-1.docker.io                                                                                                            0.0s
 => [base 1/5] FROM docker.io/library/golang:1.20-alpine@sha256:7839c9f01b5502d7cb5198b2c032857023424470b3e31ae46a8261ffca72912a                                        10.5s
 => => resolve docker.io/library/golang:1.20-alpine@sha256:7839c9f01b5502d7cb5198b2c032857023424470b3e31ae46a8261ffca72912a                                              0.0s
 => => sha256:7f9bcf943fa5571df036dca6da19434d38edf546ef8bb04ddbc803634cc9a3b8 284.71kB / 284.71kB                                                                       1.4s
 => => sha256:9fd371fdf0be1f3f0149451e08183a8bb178e63b4360e6691f07dccf51f0dc7f 100.94MB / 100.94MB                                                                       8.4s
 => => sha256:add974993529c266bf715fdeb763bf86e7a45dc0405d68fbe483a4428c59b55d 155B / 155B                                                                               0.7s
 => => sha256:7839c9f01b5502d7cb5198b2c032857023424470b3e31ae46a8261ffca72912a 1.65kB / 1.65kB                                                                           0.0s
 => => sha256:6f592e0689192b7e477313264bb190024d654ef0a08fb1732af4f4b498a2e8ad 1.16kB / 1.16kB                                                                           0.0s
 => => sha256:bf7808b93c00e08aff649e1a9a8a5ec286823750b0065c95b96a4fd13f2b33c6 5.18kB / 5.18kB                                                                           0.0s
 => => extracting sha256:7f9bcf943fa5571df036dca6da19434d38edf546ef8bb04ddbc803634cc9a3b8                                                                                0.0s
 => => extracting sha256:9fd371fdf0be1f3f0149451e08183a8bb178e63b4360e6691f07dccf51f0dc7f                                                                                2.0s
 => => extracting sha256:add974993529c266bf715fdeb763bf86e7a45dc0405d68fbe483a4428c59b55d                                                                                0.0s
 => [internal] load build context                                                                                                                                        0.0s
 => => transferring context: 18.52kB                                                                                                                                     0.0s
 => [base 2/5] WORKDIR /src                                                                                                                                              0.1s
 => [base 3/5] COPY go.mod go.sum .                                                                                                                                      0.0s
 => [base 4/5] RUN go mod download                                                                                                                                       4.0s
 => [base 5/5] COPY . .                                                                                                                                                  0.1s
 => [build-client 1/1] RUN go build -o /bin/client ./cmd/client                                                                                                         17.9s
 => [build-server 1/1] RUN go build -o /bin/server ./cmd/server                                                                                                         18.3s
 => [prod 1/2] COPY --from=build-client /bin/client /bin/                                                                                                                0.0s
 => [prod 2/2] COPY --from=build-server /bin/server /bin/                                                                                                                0.0s
 => exporting to image                                                                                                                                                   0.1s
 => => exporting layers                                                                                                                                                  0.1s
 => => writing image sha256:2e5c47c451b9e8e1c0a5478bd448384f0930945ce0833d74b682eeab26154759                                                                             0.0s
 => => naming to docker.io/library/multi:stage                                                                                                                           0.0s
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ 
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ docker images
REPOSITORY                 TAG       IMAGE ID       CREATED          SIZE
multi                      stage     2e5c47c451b9   20 seconds ago   15.9MB
ddd-book                   ch8.1     fa76995d62c3   40 minutes ago   98.1MB
kamalsulaiman97/ddd-book   ch8.1     fa76995d62c3   40 minutes ago   98.1MB
test                       latest    096d13607584   29 hours ago     101MB
ubuntu                     latest    5a81c4b8502e   3 weeks ago      77.8MB
alpine                     latest    c1aabb73d233   5 weeks ago      7.33MB
nigelpoulton/ddd-book      web0.1    bf819e4fb31b   2 months ago     95.4MB
hello-world                latest    9c7a54a9a43c   2 months ago     13.3kB
prom/snmp-generator        v0.19.0   f9953a416350   2 years ago      926MB
prom/snmp-exporter         v0.19.0   d2a8ce729b15   2 years ago      17.8MB
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ docker history multi:stage
IMAGE          CREATED              CREATED BY                          SIZE      COMMENT
2e5c47c451b9   About a minute ago   ENTRYPOINT ["/bin/server"]          0B        buildkit.dockerfile.v0
<missing>      About a minute ago   COPY /bin/server /bin/ # buildkit   7.9MB     buildkit.dockerfile.v0
<missing>      About a minute ago   COPY /bin/client /bin/ # buildkit   7.98MB    buildkit.dockerfile.v0
```
- The image file only 15MB - it only copy complied client and server binaries

# Multi images in a single Dockerfile

- You can split the **prod** stage into two stages:

```
FROM golang:1.20-alpine AS base
WORKDIR /src
COPY go.mod go.sum .
RUN go mod download
COPY . .

FROM base AS build-client
RUN go build -o /bin/client ./cmd/client

FROM base AS build-server
RUN go build -o /bin/server ./cmd/server

FROM scratch AS prod-client
COPY --from=build-client /bin/client /bin/
ENTRYPOINT [ "/bin/client" ]

FROM scratch AS prod-server
COPY --from=build-server /bin/server /bin/
ENTRYPOINT [ "/bin/server" ]
```

- Add **-f** flag when building the image:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ docker build -t multi:client --target prod-client -f Dockerfile-final .
[+] Building 1.0s (12/12) FINISHED                                                                                                                             docker:default
 => [internal] load build definition from Dockerfile-final                                                                                                               0.0s
 => => transferring dockerfile: 478B                                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/golang:1.20-alpine                                                                                                    1.0s
 => [base 1/5] FROM docker.io/library/golang:1.20-alpine@sha256:7839c9f01b5502d7cb5198b2c032857023424470b3e31ae46a8261ffca72912a                                         0.0s
 => [internal] load build context                                                                                                                                        0.0s
 => => transferring context: 457B                                                                                                                                        0.0s
 => CACHED [base 2/5] WORKDIR /src                                                                                                                                       0.0s
 => CACHED [base 3/5] COPY go.mod go.sum .                                                                                                                               0.0s
 => CACHED [base 4/5] RUN go mod download                                                                                                                                0.0s
 => CACHED [base 5/5] COPY . .                                                                                                                                           0.0s
 => CACHED [build-client 1/1] RUN go build -o /bin/client ./cmd/client                                                                                                   0.0s
 => CACHED [prod-client 1/1] COPY --from=build-client /bin/client /bin/                                                                                                  0.0s
 => exporting to image                                                                                                                                                   0.0s
 => => exporting layers                                                                                                                                                  0.0s
 => => writing image sha256:83a23f26ddf8443c28827ff17a6745e366b2d8ee967f72cf316ac842b5922f0a                                                                             0.0s
 => => naming to docker.io/library/multi:client                                                                                                                          0.0s
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ docker build -t multi:server --target prod-server -f Dockerfile-final .
[+] Building 1.0s (12/12) FINISHED                                                                                                                             docker:default
 => [internal] load build definition from Dockerfile-final                                                                                                               0.0s
 => => transferring dockerfile: 478B                                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/golang:1.20-alpine                                                                                                    1.0s
 => [base 1/5] FROM docker.io/library/golang:1.20-alpine@sha256:7839c9f01b5502d7cb5198b2c032857023424470b3e31ae46a8261ffca72912a                                         0.0s
 => [internal] load build context                                                                                                                                        0.0s
 => => transferring context: 457B                                                                                                                                        0.0s
 => CACHED [base 2/5] WORKDIR /src                                                                                                                                       0.0s
 => CACHED [base 3/5] COPY go.mod go.sum .                                                                                                                               0.0s
 => CACHED [base 4/5] RUN go mod download                                                                                                                                0.0s
 => CACHED [base 5/5] COPY . .                                                                                                                                           0.0s
 => CACHED [build-server 1/1] RUN go build -o /bin/server ./cmd/server                                                                                                   0.0s
 => CACHED [prod-server 1/1] COPY --from=build-server /bin/server /bin/                                                                                                  0.0s
 => exporting to image                                                                                                                                                   0.0s
 => => exporting layers                                                                                                                                                  0.0s
 => => writing image sha256:3cb1d130084490df37160ca13825c5b33d049a63ef44f26c28a8adbaeb936dc2                                                                             0.0s
 => => naming to docker.io/library/multi:server                                                                                                                          0.0s
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ 
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-stage$ docker images
REPOSITORY                 TAG       IMAGE ID       CREATED          SIZE
multi                      server    3cb1d1300844   2 minutes ago    7.9MB
multi                      stage     2e5c47c451b9   13 minutes ago   15.9MB
multi                      client    83a23f26ddf8   13 minutes ago   7.98MB
```


# Summary of App containerizaiton

```
$ docker build #command that reads a Dockerfile
$ Dockerfile FROM # specifies the base image for new image
$ Dockerilfe RUN #run commmands inside the image during a build
$ Dockerfile COPY #add files inside the image during a build
$ Dockerfile EXPOSE #network port configuration
$ Dockerfile ENTRYPOINT #sets the default app to run when the image is started as a container
```
