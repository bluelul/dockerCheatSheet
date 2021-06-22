# Docker Cheat Sheet
### Author: `bluelul`

## Quick Concept
- `Docker image` ~ `.gho (ghost) file` (can be OS image, pre-installed application, or both)
- `Docker container` ~ `virtual machine` (machine installed Docker image, can be accessed via shell)

## Install Docker
- Read [Docker Homepage](https://docs.docker.com/engine/install/)

## Run Docker commands without sudo
- Add the `docker` group if it doesn't already exist  
Add the connected user `$USER` to the docker group  
Restart the `docker` daemon  
```bash
sudo groupadd docker
sudo gpasswd -a $USER docker
sudo service docker restart
```
- ***Log out and log back in*** so that your group membership is re-evaluated.
- Check docker command without sudo: `docker ps -a`

## Build Hello World image
Make a folder contain docker image
```bash
mkdir hello
cd hello
```
Add a `Dockerfile` (no extension) with content:
```bash
FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y neofetch
```
Build image
```bash
docker build -t docker_hello .
```
Check image list
```bash
docker images
```

## Operate container
### Run container (create new container)
Default for hello_world example
```bash
docker run --name hello_container -it docker_hello bash
```
Default for hello_world example with host machine's share-folder mounted
```bash
cd ~/Desktop
mkdir share_docker
cd share_docker
echo hello_world > hello.txt

docker run --name hello_container --mount src=$(pwd),target=/home,type=bind -it docker_hello bash
```
Run container standalone (random name)
```bash
docker run image_name
```
Run container with specific name 
```bash
docker run --name container_name image_name
```
Run container with terminal shell, `shell_name` can be `bash`, `sh`, `zsh`,etc.
```bash
docker run -it image_name shell_name
```
Run container with mounted folder from host machine (first, `cd` to this folder)
```bash
docker run --mount src=$(pwd),target=/home,type=bind image_name
```
### List containers
Show all containers
```bash
docker ps -a
```
Show active containers
```bash
docker ps
```
### Stat containers (Docker top, monitor resource usage of containers)
```bash
docker stat
``` 
### Start container (start inactive container)
```bash
docker start container_name
```
### Restart container (stop then start an active container or start an inactive container)
```bash
docker restart container_name
```
### Open terminal shell in active container
```bash
docker exec -it container_name shell_name
```
`shell_name` can be `bash`, `sh`, `zsh`,etc.
### Kill container (shutdown container, make it inactive)
```bash
docker kill container_name
```
### Remove container (delete containter, wipe data)
Remove a container
```bash
docker rm container_name
```
Force remove a container
```bash
docker rm -f container_name
```
Remove all containers
```bash
docker rm $(docker ps -aq)
```
