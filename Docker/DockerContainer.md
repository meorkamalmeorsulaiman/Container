# Container
- It's a runtime instance of an image
- One docker image can create multiple containers
- To start docker contianer:

```
docker run <image> <app>
```
- Example:

```
docker run -it ubuntu /bin/bash
```
- Above command will start a new container based ubuntu and start a bash shell
- the flag **-it** connect current terminal to the container shell
- Container run until the main app exit, above example - the container exit when the bash shell exit
- Below demo the mechanics, start and seize terminal for 10sec then exit:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker pull alpine:latest
latest: Pulling from library/alpine
31e352740f53: Already exists 
Digest: sha256:82d1e9d7ed48a7523bdebc18cf6290bdb97b82302a8a9c27d4fe885949ea94d1
Status: Downloaded newer image for alpine:latest
docker.io/library/alpine:latest
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker run -it alpine:latest sleep 10
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

- Stop container:
```
docker stop
```

- Start container:
```
docker start
```

# Container - Continue

- In VM, it virtualize hardware (CPU, RAM, storage etc.) into package called VM
- In container, it virtualize the OS resource (processes, file system etc.) into virtual instance called container

# Container Processes

- Docker can't run without it designated process

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker run -it ubuntu:latest /bin/bash
root@93255e9d900b:/# ls -l
total 48
lrwxrwxrwx   1 root root    7 Jun 24 02:02 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 18  2022 boot
drwxr-xr-x   5 root root  360 Jul 22 16:51 dev
drwxr-xr-x   1 root root 4096 Jul 22 16:51 etc
drwxr-xr-x   2 root root 4096 Apr 18  2022 home
lrwxrwxrwx   1 root root    7 Jun 24 02:02 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Jun 24 02:02 lib32 -> usr/lib32
lrwxrwxrwx   1 root root    9 Jun 24 02:02 lib64 -> usr/lib64
lrwxrwxrwx   1 root root   10 Jun 24 02:02 libx32 -> usr/libx32
drwxr-xr-x   2 root root 4096 Jun 24 02:02 media
drwxr-xr-x   2 root root 4096 Jun 24 02:02 mnt
drwxr-xr-x   2 root root 4096 Jun 24 02:02 opt
dr-xr-xr-x 358 root root    0 Jul 22 16:51 proc
drwx------   2 root root 4096 Jun 24 02:06 root
drwxr-xr-x   5 root root 4096 Jun 24 02:06 run
lrwxrwxrwx   1 root root    8 Jun 24 02:02 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Jun 24 02:02 srv
dr-xr-xr-x  13 root root    0 Jul 22 16:51 sys
drwxrwxrwt   2 root root 4096 Jun 24 02:06 tmp
drwxr-xr-x  14 root root 4096 Jun 24 02:02 usr
drwxr-xr-x  11 root root 4096 Jun 24 02:06 var
root@93255e9d900b:/# ping www.google.com
bash: ping: command not found
root@93255e9d900b:/# ps -elf
F S UID          PID    PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root           1       0  0  80   0 -  1156 do_wai 16:51 pts/0    00:00:00 /bin/bash
4 R root          11       1  0  80   0 -  1765 -      16:51 pts/0    00:00:00 ps -elf
root@93255e9d900b:/#  
root@93255e9d900b:/# exit
exit
```

- The above, first command run with /bin/bash as the application
- When exited, it will kill the container
- You can use **CTRL+PQ** which exit the container without killing it, it leave the container running in the background

```
kamal@TS-Kamal:~$ sudo docker run -it ubuntu:latest /bin/bash
kamal@TS-Kamal:~$ sudo docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED          STATUS          PORTS     NAMES
a96ce4954c3a   ubuntu:latest   "/bin/bash"   13 seconds ago   Up 12 seconds             optimistic_kilby
```
- You can re-attach the container using:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker exec -it a96ce4954c3a bash
root@a96ce4954c3a:/# ps -elf
F S UID          PID    PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root           1       0  0  80   0 -  1156 do_sel 16:55 pts/0    00:00:00 /bin/bash
4 S root           8       0  1  80   0 -  1156 do_wai 16:58 pts/1    00:00:00 bash
4 R root          17       8  0  80   0 -  1765 -      16:58 pts/1    00:00:00 ps -elf
root@a96ce4954c3a:/# exit
exit
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED         STATUS         PORTS     NAMES
a96ce4954c3a   ubuntu:latest   "/bin/bash"   2 minutes ago   Up 2 minutes             optimistic_kilby
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```
- Why the container not terminated?
- Because the **exec** command create new process you can see **PID 8**. Hence, the container wont terminated but kill the process (8) when you exited  
- Seems like container designated process will be **PID 1**
- Stop the container using:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED         STATUS         PORTS     NAMES
a96ce4954c3a   ubuntu:latest   "/bin/bash"   2 minutes ago   Up 2 minutes             optimistic_kilby
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker stop a96ce4954c3a
a96ce4954c3a
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker rm a96ce4954c3a
a96ce4954c3a
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```

# Container lifecycle

- Lifecycle - start, run, stop & buried

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker run --name kamal -it ubuntu:latest /bin/bash
root@0622f06e27b1:/# cd tmp/
root@0622f06e27b1:/tmp# ls
root@0622f06e27b1:/tmp# ls -l
total 0
root@0622f06e27b1:/tmp# echo "Test file 1" > firstFile
root@0622f06e27b1:/tmp# cat firstFile 
Test file 1
root@0622f06e27b1:/tmp# ls -l
total 4
-rw-r--r-- 1 root root 12 Jul 22 17:06 firstFile
root@0622f06e27b1:/tmp# kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED          STATUS          PORTS     NAMES
0622f06e27b1   ubuntu:latest   "/bin/bash"   51 seconds ago   Up 50 seconds             kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker stop kamal
kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED              STATUS                        PORTS     NAMES
0622f06e27b1   ubuntu:latest   "/bin/bash"              About a minute ago   Exited (137) 14 seconds ago             kamal
5e38973b9707   ubuntu:latest   "/bin/bash -name kam…"   2 minutes ago        Exited (1) 2 minutes ago                intelligent_aryabhata
4c1230a562dc   ubuntu:latest   "/bin/bash --name ka…"   2 minutes ago        Exited (2) 2 minutes ago                awesome_stonebraker
29671bcb9ed3   ubuntu:latest   "/bin/bash"              12 minutes ago       Exited (129) 12 minutes ago             clever_raman
2782ac4dea45   ubuntu:latest   "/bin/bash"              12 minutes ago       Exited (129) 12 minutes ago             condescending_almeida
93255e9d900b   ubuntu:latest   "/bin/bash"              15 minutes ago       Exited (0) 14 minutes ago               sharp_dijkstra
48edd1ae796e   ubuntu:latest   "/bin/bash"              19 minutes ago       Exited (0) 16 minutes ago               upbeat_jackson
09ca5cc249bd   alpine:latest   "sleep 10"               38 minutes ago       Exited (0) 37 minutes ago               serene_keldysh
4ace80ce6991   test:latest     "node ./app.js"          9 hours ago          Exited (137) 9 hours ago                web1
47852674ca51   ubuntu:latest   "/bin/bash"              9 hours ago          Exited (137) 9 hours ago                blissful_hodgkin
20dd105b5f83   hello-world     "/hello"                 5 days ago           Exited (0) 5 days ago                   epic_jang
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```

- Notice from above, **ps** doesn't show any container because it was stopped
- Use flag **-a** to list all container
- The config file still exist in Docker host
- You can restart at any time:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED              STATUS                        PORTS     NAMES
0622f06e27b1   ubuntu:latest   "/bin/bash"              About a minute ago   Exited (137) 14 seconds ago             kamal
5e38973b9707   ubuntu:latest   "/bin/bash -name kam…"   2 minutes ago        Exited (1) 2 minutes ago                intelligent_aryabhata
4c1230a562dc   ubuntu:latest   "/bin/bash --name ka…"   2 minutes ago        Exited (2) 2 minutes ago                awesome_stonebraker
29671bcb9ed3   ubuntu:latest   "/bin/bash"              12 minutes ago       Exited (129) 12 minutes ago             clever_raman
2782ac4dea45   ubuntu:latest   "/bin/bash"              12 minutes ago       Exited (129) 12 minutes ago             condescending_almeida
93255e9d900b   ubuntu:latest   "/bin/bash"              15 minutes ago       Exited (0) 14 minutes ago               sharp_dijkstra
48edd1ae796e   ubuntu:latest   "/bin/bash"              19 minutes ago       Exited (0) 16 minutes ago               upbeat_jackson
09ca5cc249bd   alpine:latest   "sleep 10"               38 minutes ago       Exited (0) 37 minutes ago               serene_keldysh
4ace80ce6991   test:latest     "node ./app.js"          9 hours ago          Exited (137) 9 hours ago                web1
47852674ca51   ubuntu:latest   "/bin/bash"              9 hours ago          Exited (137) 9 hours ago                blissful_hodgkin
20dd105b5f83   hello-world     "/hello"                 5 days ago           Exited (0) 5 days ago                   epic_jang
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker start kamal
kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker exec -it kamal bash
root@0622f06e27b1:/# cd tmp/
root@0622f06e27b1:/tmp# cat firstFile 
Test file 1
root@0622f06e27b1:/tmp# exit
exit
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED         STATUS          PORTS     NAMES
0622f06e27b1   ubuntu:latest   "/bin/bash"   5 minutes ago   Up 59 seconds             kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```
- The config within the container still retain
- Proceed to stop and delete:

```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker stop kamal
kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS                        PORTS     NAMES
0622f06e27b1   ubuntu:latest   "/bin/bash"              7 minutes ago    Exited (137) 17 seconds ago             kamal
5e38973b9707   ubuntu:latest   "/bin/bash -name kam…"   8 minutes ago    Exited (1) 8 minutes ago                intelligent_aryabhata
4c1230a562dc   ubuntu:latest   "/bin/bash --name ka…"   8 minutes ago    Exited (2) 8 minutes ago                awesome_stonebraker
29671bcb9ed3   ubuntu:latest   "/bin/bash"              17 minutes ago   Exited (129) 17 minutes ago             clever_raman
2782ac4dea45   ubuntu:latest   "/bin/bash"              18 minutes ago   Exited (129) 18 minutes ago             condescending_almeida
93255e9d900b   ubuntu:latest   "/bin/bash"              21 minutes ago   Exited (0) 20 minutes ago               sharp_dijkstra
48edd1ae796e   ubuntu:latest   "/bin/bash"              25 minutes ago   Exited (0) 21 minutes ago               upbeat_jackson
09ca5cc249bd   alpine:latest   "sleep 10"               43 minutes ago   Exited (0) 43 minutes ago               serene_keldysh
4ace80ce6991   test:latest     "node ./app.js"          9 hours ago      Exited (137) 9 hours ago                web1
47852674ca51   ubuntu:latest   "/bin/bash"              9 hours ago      Exited (137) 9 hours ago                blissful_hodgkin
20dd105b5f83   hello-world     "/hello"                 5 days ago       Exited (0) 5 days ago                   epic_jang
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker rm kamal
kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS                        PORTS     NAMES
5e38973b9707   ubuntu:latest   "/bin/bash -name kam…"   8 minutes ago    Exited (1) 8 minutes ago                intelligent_aryabhata
4c1230a562dc   ubuntu:latest   "/bin/bash --name ka…"   8 minutes ago    Exited (2) 8 minutes ago                awesome_stonebraker
29671bcb9ed3   ubuntu:latest   "/bin/bash"              17 minutes ago   Exited (129) 17 minutes ago             clever_raman
2782ac4dea45   ubuntu:latest   "/bin/bash"              18 minutes ago   Exited (129) 18 minutes ago             condescending_almeida
93255e9d900b   ubuntu:latest   "/bin/bash"              21 minutes ago   Exited (0) 20 minutes ago               sharp_dijkstra
48edd1ae796e   ubuntu:latest   "/bin/bash"              25 minutes ago   Exited (0) 22 minutes ago               upbeat_jackson
09ca5cc249bd   alpine:latest   "sleep 10"               43 minutes ago   Exited (0) 43 minutes ago               serene_keldysh
4ace80ce6991   test:latest     "node ./app.js"          9 hours ago      Exited (137) 9 hours ago                web1
47852674ca51   ubuntu:latest   "/bin/bash"              9 hours ago      Exited (137) 9 hours ago                blissful_hodgkin
20dd105b5f83   hello-world     "/hello"                 5 days ago       Exited (0) 5 days ago                   epic_jang
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```

# Stopping container gracefully

- Container stop gracefully. At the backend, it **SIGTERM** on the PID 1 for 10 sec. If not jalan, then **SIGKILL**
- Stop container without giving any chances:

```
docker rm [container] -f
```
- Above, not gracefull straight to **SIGKILL**

# Container self-healing with restart policies

- Container can be automatically restart failed conatiners
- Policies apply per container
- Use it with the **docker run** command our YAML for orch - swarm/kubernetes
- Restart policies:
    - always - always restart unless it was stopped explicitly (notice the restartCount)
```
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker run --name kamal -it --restart always ubuntu:latest /bin/bash
root@499c941c85e4:/# exit
exit
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED         STATUS         PORTS     NAMES
499c941c85e4   ubuntu:latest   "/bin/bash"   8 seconds ago   Up 2 seconds             kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE           COMMAND       CREATED         STATUS         PORTS     NAMES
499c941c85e4   ubuntu:latest   "/bin/bash"   9 seconds ago   Up 3 seconds             kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker stop kamal
kamal
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker run --name kamal -it --restart always ubuntu:latest /bin/bash
root@4748a453a7fb:/# exit
exit
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ sudo docker inspect kamal | grep "Count"
        "RestartCount": 1,
                "MaximumRetryCount": 0
            "CpuCount": 0,
kamal@TS-Kamal:~/docker/lab/git/kamal/Container$ 
```
    - unless-stopped - It will not restart the container when the Docker daemon restarted. This will occured with always policies
    - on-failure - Restart if the container exit with **non-zero** exit code. It also will restart when Docker daemon restarted

# Starting docker with argument

-Example:

```
sudo docker run -d --name webTest -p 80:8080 [IMAGE]
```

- Flag **-it** start and attach to the container
- Flag **-d** start and let it run in the background. Current terminal won't change
- Flag **-p** maps Docker host port 80 to container port 8080 - port forwarding ish..
- Delete all container:

```
docker rmi $(docker ps -aq) -f
```

# Summary of Container lifesycle commands

```
$ docker run #start container
$ CTRL+PQ  #exit without terminating
$ docker ps #list started container
$ docker exec #run a new process inside a container
$ docker stop #stop a container
$ docker start #restart container
$ docker rm #delete container
$ docker inspect #show detail information about container config and runtime information
```