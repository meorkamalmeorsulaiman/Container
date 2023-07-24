# Swarm
- Clustering - manage multiple docker nodes as a cluster
- Orchestration - deploy & manage containerized apps
- Swarm is like kubernetes but easier and simple

# Swarm components
- Managers:
    - Control plane (BGP/routing instance?)
    - dispatching tasks to **worker*
    - Maintain state of cluster
- Workers:
    - Execute tasks assign by **manager*
- Configuration:
    - kept in-memory
    - always up-to-date
- TLS:
    - **Swarm* use TLS to encrypt comms, authen nodes & authorize nodes
    - Automatic key rotation
- Orchestrations:
    - **service* - atomic unit, scheduled
    - **service* like a high-level construct that got adv features
    - It's like adv container

# Build the Swarm

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
- Docker node that not part of swarm called **single-engine mode*
- When the nodes added to swarm it's call **swarm mode*
- Example, initialize the 1st manager:

```
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

```
sysadmin@mgr1:~$ docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
mo648h2f4xnfr0c8bbkarcolz *   mgr1       Ready     Active         Leader           24.0.5
sysadmin@mgr1:~$ 
```

- From the manager, get the token for **worker* or **manager* to join:

```
sysadmin@mgr1:~$ docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-eneqwj3lf2wn9p00z44cw615l 192.168.100.200:2377

sysadmin@mgr1:~$ docker swarm join-token manager
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3jfw5dzshv7t7jolnjewswesir687dkurv9e9xu9a5cme52tlf-d94xdnjwqdkeggol3a7hhmprx 192.168.100.200:2377
sysadmin@mgr1:~$
```
- Join a node as a worker:

```
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

```
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
    - **manager* identified by the `MANAGER STATUS` either **Leader* or **Reachable*
    - **worker* identified by nothing in `MANAGER STATUS`
    - The `*` in `ID` is telling whether the command was executed

# Swarm HA

- Swarm is like active/passive or hot-standby multi-manager HA
- The **leader* is the active manager in the cluster, others will be passive
- **leader* is the one sending updates/config/task to workers or in the swarm
- In case the passive **manager* or a *follower* received a command, it will forward/proxy to the **leader*
- If you got remote Docker client, and it sending commands to the passive **manager*, that command will be forward/proxy to the **leader*

# Lock feature

- When a manager restarted, it is good to ensure that they are synced with the current database/algo
- Re-joining the cluster might wipe-out current swarm configs
- Then we need to lock a swarm - autolock feature
- It forces restarted managers to present a key be joining the cluster
- Enable autolock feature:

```
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
# Remove manager from execute/run the application

- This feature let the **worker* only execute user applications
- Run the  feature on any **manager*:
```
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