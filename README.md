# Docker Cheat Sheet
### Author: `bluelul.com`

<br/>  

## Quick Concept
- `Docker image` ~ `.gho (ghost) file` (can be OS image {actually, only contains minimal OS dependencies/softwares, not included Linux kernel}, pre-installed application, or both)
- `Docker container` ~ `virtual machine` (machine which is installed a Docker image, can be accessed via shell)

<br/>  

## Install Docker
- Read [Docker Homepage](https://docs.docker.com/engine/install/)

<br/>  

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

<br/>  

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

<br/>  

## Run container (create new container)
### Hello world example
- Default for hello_world example
```bash
docker run --name hello_container -it docker_hello bash
```
- Default for hello_world example with host machine's share-folder mounted
```bash
cd ~/Desktop
mkdir share_docker
cd share_docker
echo hello_world > hello.txt

docker run --name hello_container --mount src=$(pwd),target=/home,type=bind -it docker_hello bash
```
### Basic
- Run container standalone (random name)
```bash
docker run image_name
```
- Run container with terminal shell, `shell_name` can be `bash`, `sh`, `zsh`,etc.  
  -i : interact (stdin) with container's executed command (e.g. terminal shell)  
  -t : create a pseudo [tty](https://en.wikipedia.org/wiki/Tty_(unix))
```bash
docker run -it image_name shell_name
```
### Option add to `Basic`
- Run container with specific name 
```bash
--name container_name
```
- Run container with mounted folder from host machine (first, `cd` to this folder)  
  The `target` argument can be any destination folder, it will be automatically created if not exist in `image_name` yet
```bash
--mount src=$(pwd),target=/home,type=bind
```
- Run container with mounted usb devices
```bash
--privileged -v /dev/bus/usb:/dev/bus/usb
```
```bash
# install usbutils in container to use lsusb
apt-get update
apt-get install usbutils
```
- Run container with GUI app supported (X11)
```bash
--net=host --env="DISPLAY" --volume="$HOME/.Xauthority:/home/.Xauthority:rw"
```
```bash
# disable access control of X11 on host machine to allow any client to access host machine
# this command is unsafe, just for testing, google xhost for more strictly command
xhost +
```
- Run container only one time (auto removed after `exit`)
```bash
--rm 
```

<br/>  

## Manage container
### List containers
- Show active containers
```bash
docker ps
```
- Show all containers
```bash
docker ps -a
```
- Show all containers with size usage
```bash
docker ps -as
```
- ***Note: Both `container_id` and `container_name` can be used to target a container in most Docker commands***
### Stat containers (Docker top, monitor resource usage of containers)
```bash
docker stat
``` 

<br/>  

## Operate container
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
### Stop container (properly shutdown container, make it inactive)
```bash
docker stop container_name
```
### Kill container (force shutdown container, make it inactive)
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

<br/>  

## Container and Image
### Commit container (backup/save/ghost container into an new image)
```bash
docker commit container_name image_name
```

<br/>  

## Container and Host machine
### Copy file / folder
- Copy file / folder from container to host machine
```bash
docker cp container_name:path_to_source_file_or_folder host_path_to_dest_folder
```
- Copy file / folder from host machine to container
```bash
docker cp host_path_to_source_file_or_folder container_name:path_to_dest_folder 
```
