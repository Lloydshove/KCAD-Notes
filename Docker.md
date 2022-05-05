# Docker Notes

Environment Consistency, no app conflicts, consistent deployments anywhere running docker, developer onboarding

## Create Image
Docker Image - Layered file system - code - server - env variables - security settings .  Same wherever it is running
Read only template required to create docker container instance

docker-compose build
docker-compose up
docker exec -it <container ID> sh
docker-compose up
  
### Dockerfiles
All command line calls required to make an image  
Define layered file system

```
FROM        <base image>
LABEL       <metadata>
ENV         <env variables>
WORKD IR    <working dir>
COPY        <copy from> <to>
RUN         <commands to run>
EXPOSE      <port to expose>
ENTRYPOINT  <commands to start it up>
  
# use $<env key> to reference env variable
# .dockerignore at docker file level to ignore what not to copy into the image
  
```
  
docker build -t <name> . -f <docker file name>
docker images
docker rmi #remove images
 

## Run Image
docker run -p <ext port>:<int port> -d nginx:alpine
-d = detach
--a assign name to container  
  
## Container volumes
-v <non container folder to map to>:<container folder>
-v $(pwd):/var/www/logs   < where to write to on the how, use where I run docker run from.  
  
docker logs <container id>
                                
docker run -d -p 80:80 docker/getting-started
// lots of other command line switches
  
  
docker ps -a 
docker stop <start of container id>
docker rm <start of container id>

## Cross Image Comms

Bridge network to communicate between containers.  Bridge for containers on same machine
  
docker network create --driver bridge <network name>
docker network ls
docker network rm <network name>
  
docker run -d --net=<network name> --name <name> <image name>
  
container name is used as the host name within the network to connect 
  
docker run -d --net=isoated_network --name nodeapp -p 3000:3000 -v $(pwd)/logs:/var/www/logs danwahlin/nodeapp 

docker network inspect <network id>
  
## Shell into container
docker exec -it <containerId> <command>
docker exec -it 12abc sh
  
## Docker Compose
services with yaml configuration files - build multiple images, stop/start, view status, stream logs

  
Example docker-compose.yml
```
  version: '3.x'
  services:
    app:
      ...
      networks:
        - nodeapp-network
    db:
      ...
      networks:
        - nodeapp-network
  
  networks:
    nodeapp-network:
      driver: bridge
  
```
service name becomes the network connection name
  
  docker-compose build
  docker-compose up
  docker-compose down
