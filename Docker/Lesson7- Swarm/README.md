# Swarm

- Clustering - manage multiple docker nodes as a cluster
- Orchestration - deploy & manage containerized apps
- Swarm is like kubernetes but easier and simple

## Swarm components

- Managers:
  - Control plane (BGP/routing instance?)
  - dispatching tasks to *worker*
  - Maintain state of cluster
- Workers:
  - Execute tasks assign by **manager*
- Configuration:
  - kept in-memory
  - always up-to-date
- TLS:
  - *Swarm* use TLS to encrypt comms, authen nodes & authorize nodes
  - Automatic key rotation
- Orchestrations:
  - *service* - atomic unit, scheduled
  - *service* like a high-level construct that got adv features
  - It's like adv container

## Build the Swarm

- 3 Managers
- 3 Workers
- IP Details:
  - mgrX: 192.168.100.20x
  - wrkX: 192.168.100.21x

## Initialize docker swarm

- Steps:
  - 1. Initialize the 1st manager
  - 2. Join the additional mangers
  - 3. Join the workers
- Docker node that not part of swarm called *single-engine mode*
- When the nodes added to swarm it's call *swarm mode*
- Example, initialize the 1st manager:

```bash
sysadmin@mgr1:~$ docker swarm init --advertise-addr 192.168.100.200:2377 --listen-addr 192.168.100.200:2377
Swarm initialized: current node (mo648h2f4xnfr0c8bbkarcolz) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-eneqwj3lf2wn9p00z44cw615l 192.168.100.200:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

- The `docker warm init` - to initilize a new swarm and make the node as the 1st manager
- The `--advertise-addr` - the API endpoint that going will be use by other manager and workers
- The `--listen-addr` - It is the IP address that being use to access swarp node traffic
- List nodes in the swarm:

```bash
sysadmin@mgr1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
mo648h2f4xnfr0c8bbkarcolz *   mgr1       Ready     Active         Leader           24.0.5
sysadmin@mgr1:~$ 
```

- From the manager, get the token for *worker* or *manager* to join:

```bash
sysadmin@mgr1:~$ docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-eneqwj3lf2wn9p00z44cw615l 192.168.100.200:2377

sysadmin@mgr1:~$ docker swarm join-token manager
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-d94xdnjwqdkeggol3a7hhmprx 192.168.100.200:2377
sysadmin@mgr1:~$
```

- Join a node as a worker:

```bash
sysadmin@wrk1:~$ docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-eneqwj3lf2wn9p00z44cw615l 192.168.100.200:2377
This node joined a swarm as a worker.
#wrk2
sysadmin@wrk2:~$ docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-eneqwj3lf2wn9p00z44cw615l 192.168.100.200:2377 --advertise-addr 192.160.100.211:2377 --listen-addr 192.168.100.211:2377
This node joined a swarm as a worker.
sysadmin@wrk2:~$ 
#wrk3
sysadmin@wrk3:~$  docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-eneqwj3lf2wn9p00z44cw615l 192.168.100.200:2377
This node joined a swarm as a worker.
sysadmin@wrk3:~$ 
```

- Promote a worker to manager:

```bash
sysadmin@mgr1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
mo648h2f4xnfr0c8bbkarcolz *   mgr1       Ready     Active         Leader           24.0.5
z9a1zib1wjkk3c4f01da6gm8n     mgr2       Ready     Active                          24.0.5
zczsz58z7ccy15yi1vwu785lr     mgr3       Ready     Active         Reachable        24.0.5
vykd9w1j9g4h0afhzebg0yjvu     wrk1       Ready     Active                          24.0.5
60vrp24bavibd3mnvuim0hsdq     wrk2       Ready     Active                          24.0.5
vcv7d4emygagn5xpwmmu2mgld     wrk3       Ready     Active                          24.0.5
sysadmin@mgr1:~$ docker node promote z9a1zib1wjkk3c4f01da6gm8n 
Node z9a1zib1wjkk3c4f01da6gm8n promoted to a manager in the swarm.
sysadmin@mgr1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
mo648h2f4xnfr0c8bbkarcolz *   mgr1       Ready     Active         Leader           24.0.5
z9a1zib1wjkk3c4f01da6gm8n     mgr2       Ready     Active         Reachable        24.0.5
zczsz58z7ccy15yi1vwu785lr     mgr3       Ready     Active         Reachable        24.0.5
vykd9w1j9g4h0afhzebg0yjvu     wrk1       Ready     Active                          24.0.5
60vrp24bavibd3mnvuim0hsdq     wrk2       Ready     Active                          24.0.5
vcv7d4emygagn5xpwmmu2mgld     wrk3       Ready     Active                          24.0.5
```

- Notes:
  - **manager* identified by the `MANAGER STATUS` either *Leader* or *Reachable*
  - **worker* identified by nothing in `MANAGER STATUS`
  - The `*` in `ID` is telling whether the command was executed

## Swarm HA

- Swarm is like active/passive or hot-standby multi-manager HA
- The **leader* is the active manager in the cluster, others will be passive
- **leader* is the one sending updates/config/task to workers or in the swarm
- In case the passive **manager* or a *follower* received a command, it will forward/proxy to the **leader*
- If you got remote Docker client, and it sending commands to the passive **manager*, that command will be forward/proxy to the **leader*

## Lock feature

- When a manager restarted, it is good to ensure that they are synced with the current database/algo
- Re-joining the cluster might wipe-out current swarm configs
- Then we need to lock a swarm - autolock feature
- It forces restarted managers to present a key be joining the cluster
- Enable autolock feature:

```bash
sysadmin@mgr1:~$ docker swarm update --autolock=true
Swarm updated.
To unlock a swarm manager after it restarts, run the `docker swarm unlock`
command and provide the following key:

    SWMKEY-1-Mc1bntS9FoeKqSNOm60/RQi/HS5sGvdCEBeFj+5uCOY

Please remember to store this key in a password manager, since without it you
will not be able to restart the manager.
#mgr2 restarted
sysadmin@mgr2:~$ docker swarm update --autolock=true
Swarm updated.
sysadmin@mgr2:~$ service docker restart
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to restart 'docker.service'.
Authenticating as: sysadmin
Password: 
==== AUTHENTICATION COMPLETE ===
sysadmin@mgr2:~$ docker node ls
Error response from daemon: Swarm is encrypted and needs to be unlocked before it can be used. Please use "docker swarm unlock" to unlock it.
sysadmin@mgr2:~$ docker swarm unlock
Please enter unlock key: 
sysadmin@mgr2:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
mo648h2f4xnfr0c8bbkarcolz     mgr1       Ready     Active         Leader           24.0.5
z9a1zib1wjkk3c4f01da6gm8n *   mgr2       Ready     Active         Reachable        24.0.5
zczsz58z7ccy15yi1vwu785lr     mgr3       Ready     Active         Reachable        24.0.5
vykd9w1j9g4h0afhzebg0yjvu     wrk1       Ready     Active                          24.0.5
60vrp24bavibd3mnvuim0hsdq     wrk2       Ready     Active                          24.0.5
vcv7d4emygagn5xpwmmu2mgld     wrk3       Ready     Active                          24.0.5
sysadmin@mgr2:~$ 
```

## Remove manager from execute/run the application

- This feature let the *worker* only execute user applications
- Run the  feature on any **manager*:

```bash
sysadmin@mgr1:~$ docker node update --availability drain mgr1
mgr1
sysadmin@mgr1:~$ docker node update --availability drain mgr2
mgr2
sysadmin@mgr1:~$ docker node update --availability drain mgr3
mgr3
sysadmin@mgr1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
mo648h2f4xnfr0c8bbkarcolz *   mgr1       Ready     Drain          Leader           24.0.5
z9a1zib1wjkk3c4f01da6gm8n     mgr2       Ready     Drain          Reachable        24.0.5
zczsz58z7ccy15yi1vwu785lr     mgr3       Ready     Drain          Reachable        24.0.5
vykd9w1j9g4h0afhzebg0yjvu     wrk1       Ready     Active                          24.0.5
60vrp24bavibd3mnvuim0hsdq     wrk2       Ready     Active                          24.0.5
vcv7d4emygagn5xpwmmu2mgld     wrk3       Ready     Active                          24.0.5
```

## Deploy Swarm Services

### Creating services

- Two ways of creating services:
  - Imperatively - command `service create`
  - Declarative - use stack file

- Imperative procedure example:

```bash
sysadmin@mgr1:~/lab/ddd-book$ docker service create --name web-fe \
-p 8080:8080 \
--replicas 5 \
nigelpoulton/ddd-book:web0.1
yjy0xmky6rsk6iw86bgeeym2n
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service converged 
sysadmin@mgr1:~/lab/ddd-book$
```

- The `docker service create` - to deploy a new service
- The `--name` flag to name the service
- The `-p` flag to map the port `8080` on each node to `8080` port inside of each service replica (container)
- The `--replicas` - to tell Docker that it always be 5 replicas of this service
- *leader* instantiated 5 replicas across the swarm
- All the replicas run on the *worker*
- Swarm *leader* ensure that the *desired state* maintain and a copy was stored on the cluster and replicated to every *manager*
- What is service in swarm?
  - *A service is the definition of the tasks to execute on the manager or worker nodes. It is the central structure of the swarm system and the primary root of user interaction with the swarm*
- Validate that the service is up and get to the *desired state*

```bash
sysadmin@mgr1:~$ docker service ls
ID             NAME      MODE         REPLICAS   IMAGE                          PORTS
yjy0xmky6rsk   web-fe    replicated   5/5        nigelpoulton/ddd-book:web0.1   *:8080->8080/tcp
sysadmin@mgr1:~$ docker service ps web-fe
ID             NAME       IMAGE                          NODE      DESIRED STATE   CURRENT STATE            ERROR     PORTS
jvksmd86l5bg   web-fe.1   nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 15 minutes ago             
jdd5wfzlhaen   web-fe.2   nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 15 minutes ago             
icugzzr31ao5   web-fe.3   nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 15 minutes ago             
q3nedlxqgjqp   web-fe.4   nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 15 minutes ago             
p1jd07sz6wvw   web-fe.5   nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 15 minutes ago             
sysadmin@mgr1:~$ docker service inspect --pretty web-fe

ID:             yjy0xmky6rsk6iw86bgeeym2n
Name:           web-fe
Service Mode:   Replicated
 Replicas:      5
Placement:
UpdateConfig:
 Parallelism:   1
 On failure:    pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Update order:      stop-first
RollbackConfig:
 Parallelism:   1
 On failure:    pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Rollback order:    stop-first
ContainerSpec:
 Image:         nigelpoulton/ddd-book:web0.1@sha256:7d63a71da32ee0839e562fde42b64633a40c7660dc58038c1512db448c8a2664
 Init:          false
Resources:
Endpoint Mode:  vip
Ports:
 PublishedPort = 8080
  Protocol = tcp
  TargetPort = 8080
  PublishMode = ingress 

sysadmin@mgr1:~$ 
```

### Replicated or global services

- Replicated mode *replicated* - deploys replicas as *desired*.
- If you specify 7 replicas that the swarm will replicate 7 of containers across the swarm nodes
- Replica is the default service mode for swarm
- Other mode is *global* which run single replica (container) on each node in the swarm
- Use the `--mode global` flag to the `docker service create` command
- This mean that global service wont run more than the number of nodes in the swarm (excluding drained)

## Scaling service

- Scale up for *service* become easier
- Use `docker service scale` command to scale up or increase the *replicas*
- Example:

```bash
sysadmin@mgr1:~$ docker service ls
ID             NAME      MODE         REPLICAS   IMAGE                          PORTS
yjy0xmky6rsk   web-fe    replicated   5/5        nigelpoulton/ddd-book:web0.1   *:8080->8080/tcp
sysadmin@mgr1:~$ docker service scale web-fe=10
web-fe scaled to 10
overall progress: 10 out of 10 tasks 
1/10: running   [==================================================>] 
2/10: running   [==================================================>] 
3/10: running   [==================================================>] 
4/10: running   [==================================================>] 
5/10: running   [==================================================>] 
6/10: running   [==================================================>] 
7/10: running   [==================================================>] 
8/10: running   [==================================================>] 
9/10: running   [==================================================>] 
10/10: running   [==================================================>] 
verify: Service converged 
sysadmin@mgr1:~$ docker service ls
ID             NAME      MODE         REPLICAS   IMAGE                          PORTS
yjy0xmky6rsk   web-fe    replicated   10/10      nigelpoulton/ddd-book:web0.1   *:8080->8080/tcp
sysadmin@mgr1:~$ 
```

- Check how the service were balance across the swarm node:

```bash
sysadmin@mgr1:~$ docker service ps web-fe
ID             NAME        IMAGE                          NODE      DESIRED STATE   CURRENT STATE                ERROR     PORTS
jvksmd86l5bg   web-fe.1    nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 16 hours ago                   
jdd5wfzlhaen   web-fe.2    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 16 hours ago                   
icugzzr31ao5   web-fe.3    nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 16 hours ago                   
q3nedlxqgjqp   web-fe.4    nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 16 hours ago                   
p1jd07sz6wvw   web-fe.5    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 16 hours ago                   
uyjpoylhpgnx   web-fe.6    nigelpoulton/ddd-book:web0.1   wrk3      Running         Running about a minute ago             
abbyw74yibld   web-fe.7    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running about a minute ago             
p4ck2jyqemgq   web-fe.8    nigelpoulton/ddd-book:web0.1   wrk1      Running         Running about a minute ago             
7j53ndvc8l8y   web-fe.9    nigelpoulton/ddd-book:web0.1   wrk1      Running         Running about a minute ago             
n39zbivgk3eo   web-fe.10   nigelpoulton/ddd-book:web0.1   wrk1      Running         Running about a minute ago       
```

- Swarm use *spread* algo that try to balance the replicas as much as possible
- But this balancing the replicas as the above does not take consideration of things like CPU load and etc.
- Scale down the service:

```bash
sysadmin@mgr1:~$ docker service scale web-fe=5
web-fe scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service converged 
sysadmin@mgr1:~$ docker service ps web-fe
ID             NAME        IMAGE                          NODE      DESIRED STATE   CURRENT STATE            ERROR     PORTS
jvksmd86l5bg   web-fe.1    nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 16 hours ago               
jdd5wfzlhaen   web-fe.2    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 16 hours ago               
icugzzr31ao5   web-fe.3    nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 16 hours ago               
q3nedlxqgjqp   web-fe.4    nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 16 hours ago               
p1jd07sz6wvw   web-fe.5    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 16 hours ago               
uyjpoylhpgnx   web-fe.6    nigelpoulton/ddd-book:web0.1   wrk3      Remove          Running 10 seconds ago             
abbyw74yibld   web-fe.7    nigelpoulton/ddd-book:web0.1   wrk2      Remove          Running 10 seconds ago             
p4ck2jyqemgq   web-fe.8    nigelpoulton/ddd-book:web0.1   wrk1      Remove          Running 10 seconds ago             
7j53ndvc8l8y   web-fe.9    nigelpoulton/ddd-book:web0.1   wrk1      Remove          Running 10 seconds ago             
n39zbivgk3eo   web-fe.10   nigelpoulton/ddd-book:web0.1   wrk1      Remove          Running 10 seconds ago             
sysadmin@mgr1:~$ docker service ps web-fe
ID             NAME       IMAGE                          NODE      DESIRED STATE   CURRENT STATE          ERROR     PORTS
jvksmd86l5bg   web-fe.1   nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 16 hours ago             
jdd5wfzlhaen   web-fe.2   nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 16 hours ago             
icugzzr31ao5   web-fe.3   nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 16 hours ago             
q3nedlxqgjqp   web-fe.4   nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 16 hours ago             
p1jd07sz6wvw   web-fe.5   nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 16 hours ago             
sysadmin@mgr1:~$ 
```

## Removing services

- User command `docker service rm` - this will delete the service:

```bash
sysadmin@mgr1:~$ docker service rm web-fe
web-fe
sysadmin@mgr1:~$ docker service ps web-fe
no such service: web-fe
sysadmin@mgr1:~$ docker service ls
ID        NAME      MODE      REPLICAS   IMAGE     PORTS
sysadmin@mgr1:~$ 
```

## Rolling updates

- *rollouts* - deploy new service
- Sample create new service, but first create new overlay network for the service:

```bash
sysadmin@mgr1:~$ docker network create -d overlay uber-net
n8yxqgtddyqvrve8vzjrdvym1
sysadmin@mgr1:~$ docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
084ef9f4b0a8   bridge            bridge    local
780e93d06c82   docker_gwbridge   bridge    local
2a9b1faae783   host              host      local
utctn1is3xif   ingress           overlay   swarm
4554eddbf14d   none              null      local
n8yxqgtddyqv   uber-net          overlay   swarm
sysadmin@mgr1:~$ 
```

- Watch the **SCOPE** is *swarm* which is visible to swarm cluster
- Overlay network is the L2 network extend across all swarm nodes
- All container attached to same overlay network can talk to each other
- Essentially, overlay network in swarm is like EVPN
- Sample continue:

```bash
sysadmin@mgr1:~$ docker service create --name uber-svc --network uber-net -p 8080:8080 --replicas 12 nigelpoulton/ddd-book:web0.1
9s7hvmlhvxu5d127tnlrvifdw
overall progress: 12 out of 12 tasks 
1/12: running   [==================================================>] 
2/12: running   [==================================================>] 
3/12: running   [==================================================>] 
4/12: running   [==================================================>] 
5/12: running   [==================================================>] 
6/12: running   [==================================================>] 
7/12: running   [==================================================>] 
8/12: running   [==================================================>] 
9/12: running   [==================================================>] 
10/12: running   [==================================================>] 
11/12: running   [==================================================>] 
12/12: running   [==================================================>] 
verify: Service converged 
sysadmin@mgr1:~$ 
```

- The command mostly common
- The `--network` flag assigned the service to overlay-network create named `uber-net`
- The `-p` port forwarding/published to Docker Host when the *worker* doesn't have the replica it will redirect to the node that contain the replica - *ingress* mode
- Alternatively is *host* mode which on expose the port when the service on swarm nodes running replicas

### Staged update

- Sample below update the service with new image and put some delay between 2 replicas:

```bash
sysadmin@mgr1:~$ docker service ps uber-svc
ID             NAME          IMAGE                          NODE      DESIRED STATE   CURRENT STATE           ERROR     PORTS
bl25l97rb811   uber-svc.1    nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 7 minutes ago             
hnf4neljgv1f   uber-svc.2    nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 7 minutes ago             
i0cfvtvbxbco   uber-svc.3    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 7 minutes ago             
nm2ii0r7q1hd   uber-svc.4    nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 7 minutes ago             
oe3edzytdsik   uber-svc.5    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 7 minutes ago             
p7xnwxcf1std   uber-svc.6    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 7 minutes ago             
qyvo9qolfp29   uber-svc.7    nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 7 minutes ago             
g0h3k7iykbq3   uber-svc.8    nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 7 minutes ago             
qs57bweo10ia   uber-svc.9    nigelpoulton/ddd-book:web0.1   wrk2      Running         Running 7 minutes ago             
pu4635b1hd41   uber-svc.10   nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 7 minutes ago             
sf779bbh0nq9   uber-svc.11   nigelpoulton/ddd-book:web0.1   wrk3      Running         Running 7 minutes ago             
krmq16i0wcs1   uber-svc.12   nigelpoulton/ddd-book:web0.1   wrk1      Running         Running 7 minutes ago             
sysadmin@mgr1:~$ docker service update --image nigelpoulton/ddd-book:web0.2 --update-parallelism 2 --update-delay 20s uber-svc
uber-svc
overall progress: 4 out of 12 tasks 
1/12: running   [==================================================>] 
2/12: running   [==================================================>] 
3/12: running   [==================================================>] 
4/12: running   [==================================================>] 
5/12: ready     [======================================>            ] 
6/12: ready     [======================================>            ] 
7/12:   
8/12:   
9/12:   
10/12:   
11/12:   
12/12:   
```

- Sample below shows the effect:

```bash
sysadmin@mgr1:~$ docker service ps uber-svc
ID             NAME              IMAGE                          NODE      DESIRED STATE   CURRENT STATE                 ERROR     PORTS
s2sss1gaz1mz   uber-svc.1        nigelpoulton/ddd-book:web0.2   wrk1      Running         Running 27 seconds ago                  
bl25l97rb811    \_ uber-svc.1    nigelpoulton/ddd-book:web0.1   wrk1      Shutdown        Shutdown 28 seconds ago                 
6fnbliz4dvph   uber-svc.2        nigelpoulton/ddd-book:web0.2   wrk1      Running         Running 2 minutes ago                   
hnf4neljgv1f    \_ uber-svc.2    nigelpoulton/ddd-book:web0.1   wrk1      Shutdown        Shutdown 2 minutes ago                  
6lm40bm7irgt   uber-svc.3        nigelpoulton/ddd-book:web0.2   wrk1      Running         Running 3 minutes ago                   
i0cfvtvbxbco    \_ uber-svc.3    nigelpoulton/ddd-book:web0.1   wrk2      Shutdown        Shutdown 3 minutes ago                  
3ypyki1dnnr9   uber-svc.4        nigelpoulton/ddd-book:web0.2   wrk3      Running         Running 2 minutes ago                   
nm2ii0r7q1hd    \_ uber-svc.4    nigelpoulton/ddd-book:web0.1   wrk3      Shutdown        Shutdown 2 minutes ago                  
z7qpq1zzu04x   uber-svc.5        nigelpoulton/ddd-book:web0.2   wrk2      Running         Running 2 minutes ago                   
oe3edzytdsik    \_ uber-svc.5    nigelpoulton/ddd-book:web0.1   wrk2      Shutdown        Shutdown 2 minutes ago                  
nrpjjl4iitga   uber-svc.6        nigelpoulton/ddd-book:web0.2   wrk2      Running         Running about a minute ago              
p7xnwxcf1std    \_ uber-svc.6    nigelpoulton/ddd-book:web0.1   wrk2      Shutdown        Shutdown about a minute ago             
1izz783qafcx   uber-svc.7        nigelpoulton/ddd-book:web0.2   wrk3      Running         Running 27 seconds ago                  
qyvo9qolfp29    \_ uber-svc.7    nigelpoulton/ddd-book:web0.1   wrk3      Shutdown        Shutdown 28 seconds ago                 
ove6yt8k16kz   uber-svc.8        nigelpoulton/ddd-book:web0.2   wrk1      Running         Running about a minute ago              
g0h3k7iykbq3    \_ uber-svc.8    nigelpoulton/ddd-book:web0.1   wrk1      Shutdown        Shutdown about a minute ago             
mwj6quekoekf   uber-svc.9        nigelpoulton/ddd-book:web0.2   wrk2      Running         Running about a minute ago              
qs57bweo10ia    \_ uber-svc.9    nigelpoulton/ddd-book:web0.1   wrk2      Shutdown        Shutdown about a minute ago             
igfrxugwcqhb   uber-svc.10       nigelpoulton/ddd-book:web0.2   wrk3      Running         Running about a minute ago              
pu4635b1hd41    \_ uber-svc.10   nigelpoulton/ddd-book:web0.1   wrk3      Shutdown        Shutdown about a minute ago             
878x9b1i339s   uber-svc.11       nigelpoulton/ddd-book:web0.2   wrk3      Running         Running 2 minutes ago                   
sf779bbh0nq9    \_ uber-svc.11   nigelpoulton/ddd-book:web0.1   wrk3      Shutdown        Shutdown 2 minutes ago                  
yqzjk9iodda4   uber-svc.12       nigelpoulton/ddd-book:web0.2   wrk2      Running         Running 3 minutes ago                   
krmq16i0wcs1    \_ uber-svc.12   nigelpoulton/ddd-book:web0.1   wrk1      Shutdown        Shutdown 3 minutes ago   
```

## Troubleshoot

- User `docker service logs [service name]` to view swarm service logs

## Backup and recovery

### Backup

- Config and state is stored in `/var/lib/docker/swarm` on each swarm *manager*
- A *swarm backup* is a copy of all the file in the directory
- In real-world, I guest we just revert snaphot or all of if can rebuild

## Summary of Swarm Command

```bash
$docker node ls # list all nodes in sawrm
$docker service create #create new service in swarm
$docker service ls #list running services
$docker service ps #list running replicates
$docker service inspect #details info aboud service replicas
$docker service scale #scale up/down the replica
$docker service update #update many of the properties a running services
$docker service logs #view service log
$docker service rm #delete service from swarm
```
