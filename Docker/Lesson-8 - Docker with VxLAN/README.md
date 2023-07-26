# Docker and VxLAN

## Overlay network

- There are many network types within Docker engine, similar to what we would find with hypervisor
- Similarity like:
  - Single-host/bridge/nat
  - Bridge/vswitch
  - Overlay/VxLAN
- Overlay network which a networking feature in docker that can extend L2 connectivity over layer 3 network
- Clarity - if you have 2 docker nodes that both of them sit within separate network and hosted single container.Both of the containers can use similar IP address space, even the docker nodes are separate with different network
- Sample Overlay network:

```bash
sysadmin@mgr2:~$ docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
af61edfaa4c3   bridge            bridge    local
effb2f77bd44   docker_gwbridge   bridge    local
2a9b1faae783   host              host      local
utctn1is3xif   ingress           overlay   swarm
4554eddbf14d   none              null      local
xl94yeu3v3qj   test-overlay      overlay   swarm
sysadmin@mgr2:~$ 
sysadmin@mgr2:~$ docker service create --name container-overlay --network test-overlay --replicas 3 ubuntu:latest sleep infinity
op45zd8japq87av446jtasvih
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
sysadmin@mgr2:~$ docker service ls
ID             NAME                MODE         REPLICAS   IMAGE           PORTS
op45zd8japq8   container-overlay   replicated   3/3        ubuntu:latest   
sysadmin@mgr2:~$ 
```

- Docker node #1:

```bash
sysadmin@wrk1:~$ docker inspect test-overlay 
[
    {
        "Name": "test-overlay",
        "Id": "xl94yeu3v3qjmhaks729l23q6",
        "Created": "2023-07-26T13:50:52.176263332Z",
        "Scope": "swarm",
        "Driver": "overlay",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "10.0.2.0/24",
                    "Gateway": "10.0.2.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "b651b66923ce746dd471d8668f6494ac539f43161cbfd14728874748cc4a5d81": {
                "Name": "container-overlay.1.vubqflatqz9pk9uz2eawcpzrl",
                "EndpointID": "8c51a9c7a13057da7a91e90c8770e4a3f8b9c11856122198a4fc54a814369bd0",
                "MacAddress": "02:42:0a:00:02:42",
                "IPv4Address": "10.0.2.66/24",
                "IPv6Address": ""
            },
            "lb-test-overlay": {
                "Name": "test-overlay-endpoint",
                "EndpointID": "c7d74cc684065e7c56aa2c9c491c040ee9a8f71e7fe00a871094c6c9be25dbfb",
                "MacAddress": "02:42:0a:00:02:44",
                "IPv4Address": "10.0.2.68/24",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.driver.overlay.vxlanid_list": "4098"
        },
        "Labels": {},
        "Peers": [
            {
                "Name": "b5d43f4264f3",
                "IP": "192.168.100.210"
            },
            {
                "Name": "bd83f345249f",
                "IP": "192.168.100.212"
            }
        ]
    }
]
sysadmin@wrk1:~$ 
root@b651b66923ce:/# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
192: eth0@if193: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether 02:42:0a:00:02:42 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.0.2.66/24 brd 10.0.2.255 scope global eth0
       valid_lft forever preferred_lft forever
194: eth1@if195: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:12:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet 172.18.0.3/16 brd 172.18.255.255 scope global eth1
       valid_lft forever preferred_lft forever
root@b651b66923ce:/# ping 10.0.2.65
PING 10.0.2.65 (10.0.2.65) 56(84) bytes of data.
64 bytes from 10.0.2.65: icmp_seq=1 ttl=64 time=0.263 ms
64 bytes from 10.0.2.65: icmp_seq=2 ttl=64 time=0.222 ms
64 bytes from 10.0.2.65: icmp_seq=3 ttl=64 time=0.154 ms
^C
--- 10.0.2.65 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2029ms
rtt min/avg/max/mdev = 0.154/0.213/0.263/0.044 ms
root@b651b66923ce:/# arp -a
? (172.18.0.1) at 02:42:b4:a5:40:4a [ether] on eth1
container-overlay.3.8c4zz1lvg32d9rm93fx1lkcbj.test-overlay (10.0.2.65) at 02:42:0a:00:02:41 [ether] on eth0
root@b651b66923ce:/# 
```

- Docker nodes #3:

```bash
#Container #3
sysadmin@wrk3:~$ docker inspect test-overlay 
[
    {
        "Name": "test-overlay",
        "Id": "xl94yeu3v3qjmhaks729l23q6",
        "Created": "2023-07-26T13:50:52.176035214Z",
        "Scope": "swarm",
        "Driver": "overlay",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "10.0.2.0/24",
                    "Gateway": "10.0.2.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "d3f1d1cfe8dc9c8c7c069ef1c0462577381245db8a92cbda7ecbc14ffdbd40cd": {
                "Name": "container-overlay.3.8c4zz1lvg32d9rm93fx1lkcbj",
                "EndpointID": "f1a6317b3a70267630b5993d65ff56cf54f5f881b06c1e17c30b80dbe2c91faa",
                "MacAddress": "02:42:0a:00:02:41",
                "IPv4Address": "10.0.2.65/24",
                "IPv6Address": ""
            },
            "lb-test-overlay": {
                "Name": "test-overlay-endpoint",
                "EndpointID": "6e0133eba3d1ac92ea6e39574dc4df30df3343ab435fbc256d4cc1e1833ac57f",
                "MacAddress": "02:42:0a:00:02:43",
                "IPv4Address": "10.0.2.67/24",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.driver.overlay.vxlanid_list": "4098"
        },
        "Labels": {},
        "Peers": [
            {
                "Name": "bd83f345249f",
                "IP": "192.168.100.212"
            },
            {
                "Name": "b5d43f4264f3",
                "IP": "192.168.100.210"
            }
        ]
    }
]
sysadmin@wrk3:~$ 
root@d3f1d1cfe8dc:/# arp -a
container-overlay.1.vubqflatqz9pk9uz2eawcpzrl.test-overlay (10.0.2.66) at 02:42:0a:00:02:42 [ether] on eth0
? (172.18.0.1) at 02:42:90:68:04:57 [ether] on eth1
root@d3f1d1cfe8dc:/# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
164: eth0@if165: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether 02:42:0a:00:02:41 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.0.2.65/24 brd 10.0.2.255 scope global eth0
       valid_lft forever preferred_lft forever
166: eth1@if167: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:12:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet 172.18.0.3/16 brd 172.18.255.255 scope global eth1
       valid_lft forever preferred_lft forever
root@d3f1d1cfe8dc:/# ping 10.0.2.66 -c 3
PING 10.0.2.66 (10.0.2.66) 56(84) bytes of data.
64 bytes from 10.0.2.66: icmp_seq=1 ttl=64 time=0.209 ms
64 bytes from 10.0.2.66: icmp_seq=2 ttl=64 time=0.231 ms
64 bytes from 10.0.2.66: icmp_seq=3 ttl=64 time=0.251 ms

--- 10.0.2.66 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2054ms
rtt min/avg/max/mdev = 0.209/0.230/0.251/0.017 ms
root@d3f1d1cfe8dc:/# arp -a
container-overlay.1.vubqflatqz9pk9uz2eawcpzrl.test-overlay (10.0.2.66) at 02:42:0a:00:02:42 [ether] on eth0
? (172.18.0.1) at 02:42:90:68:04:57 [ether] on eth1
root@d3f1d1cfe8dc:/# 
```

- We can see UDP 4789 on both workers (docker nodes that run the replicas/containers):

```bash
sysadmin@wrk1:~$ ss -ul
State            Recv-Q           Send-Q                                        Local Address:Port                              Peer Address:Port          Process           
UNCONN           0                0                                                   0.0.0.0:4789                                   0.0.0.0:*                               
UNCONN           0                0                                             127.0.0.53%lo:domain                                 0.0.0.0:*                               
UNCONN           0                0                          [fe80::9825:faff:fe08:ac9]%ens18:dhcpv6-client                             [::]:*                               
UNCONN           0                0                                                         *:7946                                         *:*                               
sysadmin@wrk1:~$ 
sysadmin@wrk3:~$ ss -ul
State            Recv-Q           Send-Q                                        Local Address:Port                              Peer Address:Port          Process           
UNCONN           0                0                                             127.0.0.53%lo:domain                                 0.0.0.0:*                               
UNCONN           0                0                                                   0.0.0.0:4789                                   0.0.0.0:*                               
UNCONN           0                0                                                         *:7946                                         *:*                               
UNCONN           0                0                          [fe80::54bb:28ff:fe91:770]%ens18:dhcpv6-client                             [::]:*                               
sysadmin@wrk3:~$ 
```

## Summary of Docker with VxLAN commands

```shell
$docker network create #create docker network
$docker network ls #list network created
$docker network inspect #list detail info of particular network
$docker network rm #delete network
```
