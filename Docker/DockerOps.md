#Pull docker image

docker pull ubuntu:latest


#list docker images 

docker images


#Launch container from the docker image 

docker run -it ubuntu:latest /bin/bash


#Exit docker terminal

CTRL+PQ

#list running docker 

docker ps 


#Attach terminal to running container 

docker exec -it [name or ID] bash


#Stop container 

docker stop [NAME | ID]


#list container including the dead 

docker ps -a