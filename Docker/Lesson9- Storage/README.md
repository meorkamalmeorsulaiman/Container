# Storage

## Containers - non-persistent data

- Container create r/w on top of ro images
- Each writeable container layer exists in Docker host filesystem
- All of these are located in `/var/lib/docker/[storage-driver]` - it gets deleted when we delete the container

## Containers - persistent data

- If the container need to have persistend data - use *volume*
- *volume* can be shared across different container - shared
- The *volume* still can exists when the container was deleted
- Create volume:

```bash
sysadmin@mgr1:~$ docker volume create test-vol
test-vol
sysadmin@mgr1:~$ docker volume ls
DRIVER    VOLUME NAME
local     test-vol
sysadmin@mgr1:~$ docker volume inspect test-vol 
[
    {
        "CreatedAt": "2023-07-29T04:33:50Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/test-vol/_data",
        "Name": "test-vol",
        "Options": null,
        "Scope": "local"
    }
]
sysadmin@mgr1:~$ 
root@mgr1:~# ll /var/lib/docker/volumes/
total 36
drwx-----x  3 root root   4096 Jul 29 04:33 ./
drwx--x--- 12 root root   4096 Jul 28 19:32 ../
brw-------  1 root root 253, 0 Jul 28 19:32 backingFsBlockDev
-rw-------  1 root root  32768 Jul 29 04:33 metadata.db
drwx-----x  3 root root   4096 Jul 29 04:33 test-vol/
root@mgr1:~# 
```

- The *local* driver type of volume where only storage that can only be accessible for the node that contain the volume
- The `-d` flags specify a different driver
- All volume with *local* driver can get it's own directory in `/var/lib/docker/volumes`
- Delete volume:

```bash
sysadmin@mgr1:~$ docker volume rm test-vol 
test-vol
sysadmin@mgr1:~$ docker volume ls
DRIVER    VOLUME NAME
sysadmin@mgr1:~$ 
```

- Create a container an mount to the volume to specific mountpoint:

```bash
docker run -it --name voltainer --mount source=bizvol,target=/vol alpine
```

- Above, will create volume name `bizvol` and mount it to `/vol`

## Shared storage across cluster node

- These can be use to shared a storage across nodes
- You can use plugin for this