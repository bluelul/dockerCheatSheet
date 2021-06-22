# Docker Cheat Sheet
### Author: `bluelul`

## Quick Concept
- `Docker image` ~ `.gho (ghost) file` (can be OS image, pre-installed application, or both)
- `Docker container` ~ `virtual machine` (machine installed Docker image, can be accessed via shell)

## Install Docker
- Read [Docker Homepage](https://docs.docker.com/engine/install/)

## Run Docker commands without sudo
```bash
sudo groupadd docker
sudo gpasswd -a $USER docker
sudo service docker restart
docker
```
- Add the `docker` group if it doesn't already exist
- Add the connected user `$USER` to the docker group
- Restart the `docker` daemon
- Check docker command without sudo
- ***If fail, Log out and log back in*** so that your group membership is re-evaluated.

## Build Hello World image
- Make a folder contain docker image
```bash
mkdir hello
cd hello
```
- Add a `Dockerfile` (no extension) with content:
```bash
FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y neofetch
```
- Build image
```bash
docker build -t docker_hello .
```
- Check image list
```bash
docker images
```

## Operate container
### Run container (create new container)
- Default for hello_world example
```bash
docker run --name hello_container -it docker_hello bash
```
- Run container standalone (random name)
```bash
docker run image_name
```
- Run container with specific name 
```bash
docker run --name container_name image_name
```
- Run container with terminal shell
```bash
docker run -it image_name shell_name
```
### List containers
- Show all containers
```bash
docker ps -a
```
- Show active containers
```bash
docker ps
```
### Kill container (shutdown container)
- Kill a container
```bash
docker kill container_name
```
### Remove container (delete containter)
- Remove a container
```bash
docker rm container_name
```
- Force remove a container
```bash
docker rm -f container_name
```
- Remove all containers
```bash
docker rm $(docker ps -aq)
```
