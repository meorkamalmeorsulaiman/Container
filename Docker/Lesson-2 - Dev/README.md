# Starting an Containerized App

Docker file tells Docker how to build the app and dependencies into a Docker image.

1. Create new image using instructions in the DockerFile

```
docker build -t [IMAGE].
```

2. Check images when the build completes

```
docker ps
```

3. Run a container from the image and test the app

```
docker run -d --name web1 --publish 8080:8080 test:latest
```

4. Test the js app
5. Stop that container

```
docker stop [NAME | ID]
```

