# Compose
- Allo us to describe everything in a declarative config file
- Why?
- Cloud-native apps comes with multiple smaller service that interact and build a useful apps
- These small services called microservices
- Compse help to deploy and manage all of the microservices easily
- Compose ship with Docker engine:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ docker compose version 
Docker Compose version v2.19.1
```

# Files
- Compose use YAML files to deifine microservices application
- Default compose file is `compose.yaml` you can use `-f` to specify a custom filename
- Sample compose file:

```
networks:
  counter-net:

volumes:
  counter-vol:
  
services:
  web-fe:
    build: .
    command: python app.py
    ports:
      - target: 8080
        published: 5001
    networks:
      - counter-net
    volumes:
      - type: volume
        source: counter-vol
        target: /app
  redis:
    image: "redis:alpine"
    networks:
      counter-net:
```

# Deploy with Compose

- You need a directory that consists of:
    - compose.yaml
    - app - contain the application codes
    - Dockerfile - to build the image
    - requirement.txt - app dependencies
- These dir will be your project, and compose will use the dir name as the project name and prepend all resource names with the dir name
- Bring up the app:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose up &
[1] 8876
[+] Building 4.0s (9/9) FINISHED                                                                                                                                   
 => [web-fe internal] load build definition from Dockerfile                                                                                                   0.0s
 => => transferring dockerfile: 327B                                                                                                                          0.0s
 => [web-fe internal] load .dockerignore                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                               0.0s
 => [web-fe internal] load metadata for docker.io/library/python:alpine                                                                                       1.0s
 => [web-fe internal] load build context                                                                                                                      0.0s
 => => transferring context: 687B                                                                                                                             0.0s
 => [web-fe 1/4] FROM docker.io/library/python:alpine@sha256:25df32b602118dab046b58f0fe920e3301da0727b5b07430c8bcd4b139627fdc                                 0.0s
 => CACHED [web-fe 2/4] COPY . /app                                                                                                                           0.0s
 => CACHED [web-fe 3/4] WORKDIR /app                                                                                                                          0.0s
 => [web-fe 4/4] RUN pip install -r requirements.txt                                                                                                          2.9s
 => [web-fe] exporting to image                                                                                                                               0.1s 
 => => exporting layers                                                                                                                                       0.1s 
 => => writing image sha256:8a53dc8c1696a19d4eb1bf386575d632c6d2ef0c2f704b215ea829aacb6dab16                                                                  0.0s 
 => => naming to docker.io/library/multi-contianer-web-fe                                                                                                     0.0s 
[+] Running 4/1                                                                                                                                                    
 ✔ Network multi-contianer_counter-net   Created                                                                                                              0.1s 
 ✔ Volume "multi-contianer_counter-vol"  Created                                                                                                              0.0s 
 ✔ Container multi-contianer-redis-1     Created                                                                                                              0.0s 
 ✔ Container multi-contianer-web-fe-1    Created                                                                                                              0.0s 
Attaching to multi-contianer-redis-1, multi-contianer-web-fe-1
multi-contianer-redis-1   | 1:C 24 Jul 2023 14:41:05.390 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
multi-contianer-redis-1   | 1:C 24 Jul 2023 14:41:05.390 # Redis version=7.0.12, bits=64, commit=00000000, modified=0, pid=1, just started
multi-contianer-redis-1   | 1:C 24 Jul 2023 14:41:05.390 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:41:05.390 * monotonic clock: POSIX clock_gettime
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:41:05.390 * Running mode=standalone, port=6379.
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:41:05.390 # Server initialized
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:41:05.390 # WARNING Memory overcommit must be enabled! Without it, a background save or replication may fail under low memory condition. Being disabled, it can can also cause failures without low memory condition, see https://github.com/jemalloc/jemalloc/issues/1328. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:41:05.391 * Ready to accept connections
multi-contianer-web-fe-1  |  * Serving Flask app 'app'
multi-contianer-web-fe-1  |  * Debug mode: on
multi-contianer-web-fe-1  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
multi-contianer-web-fe-1  |  * Running on all addresses (0.0.0.0)
multi-contianer-web-fe-1  |  * Running on http://127.0.0.1:8080
multi-contianer-web-fe-1  |  * Running on http://172.18.0.3:8080
multi-contianer-web-fe-1  | Press CTRL+C to quit
multi-contianer-web-fe-1  |  * Restarting with stat
multi-contianer-web-fe-1  |  * Debugger is active!
multi-contianer-web-fe-1  |  * Debugger PIN: 132-779-425
```
- The `compose up` command is the common way to bring up Compose app
- The `&` give the terminal back
- Normally you will run it in the background using `--detach` flag
- Installed images & running the containers:
```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker images
REPOSITORY                 TAG       IMAGE ID       CREATED         SIZE
multi-contianer-web-fe     latest    8a53dc8c1696   2 minutes ago   70.9MB
multi                      server    3cb1d1300844   25 hours ago    7.9MB
multi                      stage     2e5c47c451b9   25 hours ago    15.9MB
multi                      client    83a23f26ddf8   25 hours ago    7.98MB
kamalsulaiman97/ddd-book   ch8.1     fa76995d62c3   26 hours ago    98.1MB
ddd-book                   ch8.1     fa76995d62c3   26 hours ago    98.1MB
test                       latest    096d13607584   2 days ago      101MB
redis                      alpine    c1dc010e6f24   13 days ago     30.2MB
ubuntu                     latest    5a81c4b8502e   3 weeks ago     77.8MB
alpine                     latest    c1aabb73d233   5 weeks ago     7.33MB
nigelpoulton/ddd-book      web0.1    bf819e4fb31b   2 months ago    95.4MB
hello-world                latest    9c7a54a9a43c   2 months ago    13.3kB
prom/snmp-generator        v0.19.0   f9953a416350   2 years ago     926MB
prom/snmp-exporter         v0.19.0   d2a8ce729b15   2 years ago     17.8MB
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS                                       NAMES
b88c8c62e508   multi-contianer-web-fe   "python app/app.py p…"   8 minutes ago   Up 8 minutes   0.0.0.0:5001->8080/tcp, :::5001->8080/tcp   multi-contianer-web-fe-1
a59909c2d477   redis:alpine             "docker-entrypoint.s…"   8 minutes ago   Up 8 minutes   6379/tcp                                    multi-contianer-redis-1
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker exec -it multi-contianer-web-fe-1 sh
/app # ls -l
total 20
-rw-rw-r--    1 root     root           288 Jul 23 12:50 Dockerfile
-rw-rw-r--    1 root     root           332 Jul 23 12:50 README.md
drwxrwxr-x    4 root     root          4096 Jul 24 14:41 app
-rw-rw-r--    1 root     root           355 Jul 23 12:50 compose.yaml
-rw-rw-r--    1 root     root            18 Jul 23 12:50 requirements.txt
/app # ps -elf
PID   USER     TIME  COMMAND
    1 root      0:00 python app/app.py python app.py
    7 root      0:00 /usr/local/bin/python app/app.py python app.py
   32 root      0:00 sh
   39 root      0:00 ps -elf
/app # 
```

- Other serivces from top-level keys in compose file:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker network ls
NETWORK ID     NAME                          DRIVER    SCOPE
af1a92f7041f   bridge                        bridge    local
50c4507d47cb   host                          host      local
f4b8c6d273ef   multi-contianer_counter-net   bridge    local
d8a35f4f6ddc   none                          null      local
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume ls
DRIVER    VOLUME NAME
local     e53fb359fe79c63b0fa418634c25a84404cd7ae3fcc3ec3048536757eb401316
local     multi-contianer_counter-vol
```

# Managing apps with Compose

- Bring the app down:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose down
multi-contianer-redis-1   | 1:signal-handler (1690210489) Received SIGTERM scheduling shutdown...
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:54:50.006 # User requested shutdown...
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:54:50.006 * Saving the final RDB snapshot before exiting.
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:54:50.007 * DB saved on disk
multi-contianer-redis-1   | 1:M 24 Jul 2023 14:54:50.007 # Redis is now ready to exit, bye bye...
[+] Running 0/2
 ⠙ Container multi-contianer-web-fe-1  Stopping                                                                                                                        0.2s 
[+] Running 1/2lti-contianer-redis-1   Stopping                                                                                                                        0.2s 
[+] Running 3/3lti-contianer-web-fe-1  Stopping                                                                                                                        0.3s 
 ✔ Container multi-contianer-web-fe-1   Removed                                                                                                                        0.4s 
 ✔ Container multi-contianer-redis-1    Removed                                                                                                                        0.3s 
 ✔ Network multi-contianer_counter-net  Removed                                                                                                                        0.4s 
[1]+  Done                    docker compose up
```

- Above, stopped and removed the containers & network
- The volume still there and not deleted by default:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume ls
DRIVER    VOLUME NAME
local     e53fb359fe79c63b0fa418634c25a84404cd7ae3fcc3ec3048536757eb401316
local     multi-contianer_counter-vol
```
- Use `--volumes` flags with `docker compose down` to delete all associated volumes
- Images create with docker compose will retain unless the `--rmi all` flags was used which remove all images built or pulled
- Bring the up again in background:

```kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose up --detach
[+] Running 3/3
 ✔ Network multi-contianer_counter-net  Created                                                                                                                        0.1s 
 ✔ Container multi-contianer-redis-1    Started                                                                                                                        0.2s 
 ✔ Container multi-contianer-web-fe-1   Started 
 ```
- List all process running on each containers (PID are from Docker host and not from the container):

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose top
multi-contianer-redis-1
UID     PID     PPID    C    STIME   TTY   TIME       CMD
vault   35871   35793   0    23:01   ?     00:00:00   redis-server *:6379   

multi-contianer-web-fe-1
UID    PID     PPID    C    STIME   TTY   TIME       CMD
root   35884   35854   0    23:01   ?     00:00:00   python app/app.py python app.py                  
root   35952   35884   0    23:01   ?     00:00:00   /usr/local/bin/python app/app.py python app.py  
```

- Stopping the app:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose stop
[+] Stopping 2/2
 ✔ Container multi-contianer-redis-1   Stopped                                                                                                                         0.3s 
 ✔ Container multi-contianer-web-fe-1  Stopped                                                                                                                         0.4s 
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose ps
NAME                IMAGE               COMMAND             SERVICE             CREATED             STATUS              PORTS
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose ps -a
NAME                       IMAGE                    COMMAND                  SERVICE             CREATED             STATUS                      PORTS
multi-contianer-redis-1    redis:alpine             "docker-entrypoint.s…"   redis               3 minutes ago       Exited (0) 40 seconds ago   
multi-contianer-web-fe-1   multi-contianer-web-fe   "python app/app.py p…"   web-fe              3 minutes ago       Exited (0) 40 seconds ago   
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker  ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS                      PORTS     NAMES
9ff66b9a4684   redis:alpine             "docker-entrypoint.s…"   4 minutes ago   Exited (0) 46 seconds ago             multi-contianer-redis-1
82b638a1b3d6   multi-contianer-web-fe   "python app/app.py p…"   4 minutes ago   Exited (0) 46 seconds ago             multi-contianer-web-fe-1
55e6832ebb35   ddd-book:ch8.1           "node ./app.js"          26 hours ago    Exited (137) 25 hours ago             c1
```

- Delete container:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose rm
? Going to remove multi-contianer-redis-1, multi-contianer-web-fe-1 No
```

- Restart the app:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose restart 
[+] Restarting 2/2
 ✔ Container multi-contianer-web-fe-1  Started                                                                                                                         0.2s 
 ✔ Container multi-contianer-redis-1   Started                                                                                                                         0.3s 
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose ls
NAME                STATUS              CONFIG FILES
multi-contianer     running(2)          /home/kamal/docker/lab/git/ddd-book/multi-contianer/compose.yaml
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose ps
NAME                       IMAGE                    COMMAND                  SERVICE             CREATED             STATUS              PORTS
multi-contianer-redis-1    redis:alpine             "docker-entrypoint.s…"   redis               8 minutes ago       Up 33 seconds       6379/tcp
multi-contianer-web-fe-1   multi-contianer-web-fe   "python app/app.py p…"   web-fe              8 minutes ago       Up 33 seconds       0.0.0.0:5001->8080/tcp, :::5001->8080/tcp
kamal@TS-
```

- There are diff in above output...
- Remove everything:

```kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker compose down --volumes --rmi all
[+] Running 1/0
 ✔ Volume multi-contianer_counter-vol  Removed                                                                                                                         0.0s 
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
af1a92f7041f   bridge    bridge    local
50c4507d47cb   host      host      local
d8a35f4f6ddc   none      null      local
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume ls
DRIVER    VOLUME NAME
local     e53fb359fe79c63b0fa418634c25a84404cd7ae3fcc3ec3048536757eb401316
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ 
```

# Volume

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume ls
DRIVER    VOLUME NAME
local     e1bfb3e85f087326636cfbff14ad7fa490bb9586b2b1032707666b87231cdaf2
local     e53fb359fe79c63b0fa418634c25a84404cd7ae3fcc3ec3048536757eb401316
local     multi-contianer_counter-vol
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume inspect multi-contianer_counter-vol
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume ls
DRIVER    VOLUME NAME
local     e1bfb3e85f087326636cfbff14ad7fa490bb9586b2b1032707666b87231cdaf2
local     e53fb359fe79c63b0fa418634c25a84404cd7ae3fcc3ec3048536757eb401316
local     multi-contianer_counter-vol
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume inspect multi-contianer_counter-vol
[
    {
        "CreatedAt": "2023-07-24T23:13:20+08:00",
        "Driver": "local",
        "Labels": {
            "com.docker.compose.project": "multi-contianer",
            "com.docker.compose.version": "2.19.1",
            "com.docker.compose.volume": "counter-vol"
        },
        "Mountpoint": "/var/lib/docker/volumes/multi-contianer_counter-vol/_data",
        "Name": "multi-contianer_counter-vol",
        "Options": null,
        "Scope": "local"
    }
]
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ cat compose.yaml 
networks:
  counter-net:

volumes:
  counter-vol:
  
services:
  web-fe:
    build: .
    command: python app.py
    ports:
      - target: 8080
        published: 5001
    networks:
      - counter-net
    volumes:
      - type: volume
        source: counter-vol
        target: /app
  redis:
    image: "redis:alpine"
    networks:
      counter-net:

kamal@TS-Kamal:~/docker/lab/git/ddd-book/
```

- Compose create it if it doesn't exist
- Compose will build before deploying service
- In the `compose.yaml` file, we see the /app was mounted to the volume blaa2..
- The app is actually running on Docker volume, which you can changes anything inside the volume without going into the container
- Docker volume exists in Docker host's file system:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume ls
DRIVER    VOLUME NAME
local     e1bfb3e85f087326636cfbff14ad7fa490bb9586b2b1032707666b87231cdaf2
local     e53fb359fe79c63b0fa418634c25a84404cd7ae3fcc3ec3048536757eb401316
local     multi-contianer_counter-vol
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ docker volume inspect multi-contianer_counter-vol | grep Mount
        "Mountpoint": "/var/lib/docker/volumes/multi-contianer_counter-vol/_data",
amal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ sudo ls /var/lib/docker/volumes/multi-contianer_counter-vol/_data
app  compose.yaml  Dockerfile  README.md  requirements.txt
```
- Copy the file to mounted Docker host's file system to make changes to Docker volume:

```
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ sudo ls /var/lib/docker/volumes/multi-contianer_counter-vol/_data/app/templates
error.html  index.html
kamal@TS-Kamal:~/docker/lab/git/ddd-book/multi-contianer$ sudo cp app/templates/index.html /var/lib/docker/volumes/multi-contianer_counter-vol/_data/app/templates
```

- See the effect, you can update config on the fly with container!

# Summary of Compose command

```
$docker compose up #deploy a Compose app
$docker compose stop #will stop all containers in a Compose app
$docker compose rm #will stop all containers in a Compose app and delete containers and networks but not the volumes
$docker compose restart # will restart a Compose app, if changes to Compose app while it was stopped. Change will not appread when it restarted - re-deploye it
$docker compose ps #list each container in the Compose app
$docker compose down #stop and delete a running Compose app
```
