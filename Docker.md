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
  
### Dockerhub
  

## Run Image
docker run -d -p 80:80 docker/getting-started
docker ps -a 
docker stop <start of container id>
docker rm <start of container id>

## Cross Image Comms

