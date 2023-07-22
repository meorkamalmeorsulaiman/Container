# Images 

- Read-only package
- Includes:
    - app codes
    - app dependencies
    - minimal set of OS construct and metadata
- Similarity with VM:
    - image similar to VM template
    - VM template is a stopped VM
    - Image is a stopped container
- Inside image:
    - cut-down OS and all of files and dependencies required to run an application 

# Images and containers

- You can stop a container and create a new image from it
- Image is a build-time contruct and continer is a run-time construct
- When you start an container from an image, the two construct become dependent from each other
- You can't delete an image until the last container stopped and destroyed
- Image doesn't have a kernel - container is a shared kernel with the host OS

# Pulling images

- Need to pull to local repo
- use:

```
docker images
```
- Pulling is a process of getting image to docker host

```
docker pull redis:latest
```

# Image naming and tagging

- You have to specify what image you want to pull from offical or unofficial repositories
- Below is the format of pulling an image:

```
docker pull <repository>:<tag>
```
- Below is the format of pulling an image with different version:

```
 docker pull mongo:4.2.24

#Pull the image tagged as `4.2.24` from the official `mongo` repository.

 docker pull busybox:glibc
#Pull the image tagged as `glibc` from the official `busybox` repository.

$ docker pull alpine
//Pull the image tagged as `latest` from the official `alpine` repository.
```
- if **latest** not specify, docker with automatically pull the image with latest tag. However, if the repo doesn't contain the image tag then the command will failed

# Docker with multiple tags

- Tag can be many for an image
- The tag **latest** is just arbitrary
- Filtering out images which display dangling images:

```
docker images --filter dangling=true
```

- Filter supported:
    - dangling
    - before
    - since
    - label
- All other filter, you can use reference

```
docker images --filter=reference="*:latest"
```

# Image commands

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker images --format "{{.Size}}"
101MB
77.8MB
13.3kB
926MB
17.8MB
```

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker images --format "{{.Repository}}: {{.Tag}}: {{.Size}}"
test: latest: 101MB
ubuntu: latest: 77.8MB
hello-world: latest: 13.3kB
prom/snmp-generator: v0.19.0: 926MB
prom/snmp-exporter: v0.19.0: 17.8MB
```

# Searching images locally
- Images pulled from Docker hub by default
- You can search locally using docker CLI:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker search snmp
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
objectscale/snmp-notifier                                                         0                    
edgexfoundry/device-snmp          Device service to connect to SNMP protocol d…   0                    
edgexfoundry/docker-device-snmp   ARCHIVED! The legacy device service to conne…   0                    
edgexfoundry/device-snmp-arm64    ARM64 device service to connect to SNMP prot…   2                    
polinux/snmpd                     SNMP command-like ready (CentOS-7)              13                   [OK]
really/snmpd                      Tiny net-snmp daemon - snmpd - for situation…   4                    [OK]
prom/snmp-exporter                                                                23                   
crazymax/snmpd                    SNMP daemon image based on Alpine Linux         0                    
maxwo/snmp-notifier               A webhook to relay Prometheus alerts as SNMP…   2                    
ricardbejarano/snmp_exporter      🔥 Built-from-source container image of snmp…   6                    
tonimoreno/snmpcollector          A full featured Generic SNMP data collector …   4                    
digiwhite/snmpd                   snmpd for CoreOS                                0                    [OK]
vanimesh/snmptrapd_with_snmptt    SNMPTrapd with snmptt                           1                    
tandrup/snmpsim                   SNMP simulator                                  4                    [OK]
oakman/snmp-exporter              Docker image of Prometheus snmp_exporter pro…   0                    [OK]
amkay/snmp_exporter               SNMP Exporter for Prometheus                    0                    [OK]
cristal2xs/snmp                   A simple SNMP Agent (not for use in producti…   1                    
vaporio/snmp-emulator             An SNMP emulator for testing & development      0                    
prom/snmp-generator                                                               4                    
sevendollar/snmpwalk                                                              1                    
tnwinc/snmp                                                                       2                    [OK]
dchesterton/snmp2mqtt             Expose SNMP sensors to MQTT                     0                    
xeemetric/snmp-simulator          A simple SNMP Simulator driven by agent's sn…   0                    [OK]
vaporio/snmp-plugin                                                               0                    
ibmuttam/snmp-exporter            SNMP exporter to get health status of device    0 
```
```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker search redis --filter "is-official=true"
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
redis     Redis is an open source key-value store that…   12227     [OK] 
```
# Image layer

- Image is a collection of layer - loosely-connected and read-only
- Each layer comprise one or more file

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker inspect ubuntu
[
    {
        "Id": "sha256:5a81c4b8502e4979e75bd8f91343b95b0d695ab67f241dbed0d1530a35bde1eb",
        "RepoTags": [
            "ubuntu:latest"
        ],
        "RepoDigests": [
            "ubuntu@sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-06-28T08:37:42.319109064Z",
        "Container": "c4538cadf1a220e2923eb9a63779ce0868b8801010a9288177f1b1344275a11f",
        "ContainerConfig": {
            "Hostname": "c4538cadf1a2",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/bin/bash\"]"
            ],
            "Image": "sha256:7bf4099f59a20a3871755d51471125566ce0eb5cecf1a197500ae491eb692902",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
        "DockerVersion": "20.10.21",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "sha256:7bf4099f59a20a3871755d51471125566ce0eb5cecf1a197500ae491eb692902",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 77812686,
        "VirtualSize": 77812686,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/761ec0eeace899b70d56442955b4fa4c2ba29966b881d9eb8ec5b50628539def/merged",
                "UpperDir": "/var/lib/docker/overlay2/761ec0eeace899b70d56442955b4fa4c2ba29966b881d9eb8ec5b50628539def/diff",
                "WorkDir": "/var/lib/docker/overlay2/761ec0eeace899b70d56442955b4fa4c2ba29966b881d9eb8ec5b50628539def/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:59c56aee1fb4dbaeb334aef06088b49902105d1ea0c15a9e5a2a9ce560fa4c5d"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

- Docker image start with base layer, and continue to add as changes made

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker pull nginx:latest
latest: Pulling from library/nginx
faef57eae888: Pull complete *Layer*
76579e9ed380: Pull complete 
cf707e233955: Pull complete 
91bb7937700d: Pull complete 
4b962717ba55: Pull complete 
f46d7b05649a: Pull complete 
103501419a0a: Pull complete 
Digest: sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```
```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker history ubuntu
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
5a81c4b8502e   3 weeks ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      3 weeks ago   /bin/sh -c #(nop) ADD file:140fb5108b4a2861b…   77.8MB    
<missing>      3 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      3 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      3 weeks ago   /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B        
<missing>      3 weeks ago   /bin/sh -c #(nop)  ARG RELEASE                  0B        
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```

# Sharing image layers

- Different image can share their layers
- When you pulling an image, docker can recognize which layer already exist in the local copy   

# Pulling image by digest

- To fix pulling image with tags
- All image get hash
- It's the content of the image itself inform of hash
- Each time you pull, the command include the image hash 

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker images --digests ubuntu
REPOSITORY   TAG       DIGEST                                                                    IMAGE ID       CREATED       SIZE
ubuntu       latest    sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508   5a81c4b8502e   3 weeks ago   77.8MB
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```

- Test pulling using digest:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker rmi nginx:latest
Untagged: nginx:latest
Untagged: nginx@sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef
Deleted: sha256:021283c8eb95be02b23db0de7f609d603553c6714785e7a673c6594a624ffbda
Deleted: sha256:a9de33035096cdf7bbaf7f3e1291701c0620d2a0e66152228abef35a79002876
Deleted: sha256:d66c35807d98c6f37bd2a14c6506a42d27a40fbdb564e233f7a78aafdc636c59
Deleted: sha256:a4c423818ed6dc12a545c349d0dc36a5695446448e07229e96c7235a126c2520
Deleted: sha256:c04094edc9df98c870e281f3b947a7782ca6d542d8715814ac06786466af3659
Deleted: sha256:c9c467815e8fe87d99f0f500495cf7f4f9096cf6c116ef2782e84bb17a4a5e06
Deleted: sha256:4645f26713fbea51190f5de52b88fbe27b42efd61c0dba87c81fa16df9a8f649
Deleted: sha256:24839d45ca455f36659219281e0f2304520b92347eb536ad5cc7b4dbb8163588
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```
```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker pull nginx@sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef
docker.io/library/nginx@sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef: Pulling from library/nginx
faef57eae888: Pull complete 
76579e9ed380: Pull complete 
cf707e233955: Pull complete 
91bb7937700d: Pull complete 
4b962717ba55: Pull complete 
f46d7b05649a: Pull complete 
103501419a0a: Pull complete 
Digest: sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef
Status: Downloaded newer image for nginx@sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef
docker.io/library/nginx@sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker images --digests nginx
REPOSITORY   TAG       DIGEST                                                                    IMAGE ID       CREATED       SIZE
nginx        <none>    sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef   021283c8eb95   2 weeks ago   187MB
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```

# Docker image for multi-arch - x86/ARM/etc..

- You can use the pull command from any arch and docker will pull the correct image
- Two construct:
    - manifest lists
    - manifest
- Like list of architecture supported by an image tag

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker manifest inspect nginx | grep "architecture"
            "architecture": "amd64",
            "architecture": "arm",
            "architecture": "arm",
            "architecture": "arm64",
            "architecture": "386",
            "architecture": "mips64le",
            "architecture": "ppc64le",
            "architecture": "s390x",
```

# Deleting image

- Delete image using:

```
docker rmi
```

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker images
REPOSITORY            TAG       IMAGE ID       CREATED        SIZE
test                  latest    096d13607584   7 hours ago    101MB
nginx                 latest    021283c8eb95   2 weeks ago    187MB
ubuntu                latest    5a81c4b8502e   3 weeks ago    77.8MB
hello-world           latest    9c7a54a9a43c   2 months ago   13.3kB
prom/snmp-generator   v0.19.0   f9953a416350   2 years ago    926MB
prom/snmp-exporter    v0.19.0   d2a8ce729b15   2 years ago    17.8MB
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker rmi 021283c8eb95 
Untagged: nginx:latest
Untagged: nginx@sha256:08bc36ad52474e528cc1ea3426b5e3f4bad8a130318e3140d6cfe29c8892c7ef
Deleted: sha256:021283c8eb95be02b23db0de7f609d603553c6714785e7a673c6594a624ffbda
Deleted: sha256:a9de33035096cdf7bbaf7f3e1291701c0620d2a0e66152228abef35a79002876
Deleted: sha256:d66c35807d98c6f37bd2a14c6506a42d27a40fbdb564e233f7a78aafdc636c59
Deleted: sha256:a4c423818ed6dc12a545c349d0dc36a5695446448e07229e96c7235a126c2520
Deleted: sha256:c04094edc9df98c870e281f3b947a7782ca6d542d8715814ac06786466af3659
Deleted: sha256:c9c467815e8fe87d99f0f500495cf7f4f9096cf6c116ef2782e84bb17a4a5e06
Deleted: sha256:4645f26713fbea51190f5de52b88fbe27b42efd61c0dba87c81fa16df9a8f649
Deleted: sha256:24839d45ca455f36659219281e0f2304520b92347eb536ad5cc7b4dbb8163588
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker images
REPOSITORY            TAG       IMAGE ID       CREATED        SIZE
test                  latest    096d13607584   7 hours ago    101MB
ubuntu                latest    5a81c4b8502e   3 weeks ago    77.8MB
hello-world           latest    9c7a54a9a43c   2 months ago   13.3kB
prom/snmp-generator   v0.19.0   f9953a416350   2 years ago    926MB
prom/snmp-exporter    v0.19.0   d2a8ce729b15   2 years ago    17.8MB
```

- It's remove the images and all of the layers
- What about shared layers?
- Those that shared, it wont be deleted
- Delete multiple images:

```
docker rmi [ID No.1] [ID No. 2]
```

# Summary of Image commands

```
$ docker pull #download/pull image to local docker host
$ docker images #list image in local copy
$ docker inspect #list layer
$ docker manifest inspect #list manifest a.k.a arch
$ docker rmi #delete image
```



