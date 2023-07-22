# Operating Docker - download image, start, log in, run command & destroy it

1. Pull docker image

```
docker pull ubuntu:latest
```

2. list docker images 

```
docker images
```

3. Launch container from the docker image 

```
docker run -it ubuntu:latest /bin/bash
```

4. Exit docker terminal

```
CTRL+PQ
```

5. list running docker 

```
docker ps 
```

6. Attach terminal to running container 

```
docker exec -it [name or ID] bash
```

7. Stop container 

```
docker stop [NAME | ID]
```

8. list container including the dead 

```
docker ps -a
```
