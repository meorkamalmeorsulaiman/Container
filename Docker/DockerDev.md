# Starting an Containerized App

# Docker file -  tells Docker how to build the app and dependencies into a Docker image.

# Create new image using instructions in the DockerFile

```
docker build -t [IMAGE] .
```



#Check images when the build completes

```
docker ps
```

#Run a container from the image and test the app

```
docker run -d --name web1 --publish 8080:8080 test:latest
```



