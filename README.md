# Docker Cheat Sheet
### Author: `bluelul.com`

<br/>  

## Quick Concept
- `Docker image` ~ `.gho (ghost) file` , can be:  
  - OS image {actually, only contains minimal OS dependencies/softwares, not included Linux kernel}
  - pre-installed application on an OS image
- `Docker container` ~ `virtual machine` (machine which is installed a Docker image, can be accessed via shell)

<br/>  

## Install Docker
- Read [Docker Homepage](https://docs.docker.com/engine/install/)

<br/>  

## Run Docker commands without sudo
### Config system
Add the `docker` group if it doesn't already exist  
```bash
sudo groupadd docker
```
Add the connected user `$USER` to the docker group    
```bash
sudo gpasswd -a $USER docker
```
### Reload system
- Option 1: restart your computer
- Option 2:
  - Restart the `docker` daemon
  ```bash
  sudo service docker restart
  ```
  - ***Log out and log back in*** so that your group membership is re-evaluated.

### Verify
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
- Add environment variable to container when startup
```bash
-e ENV_NAME=env_value
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
### Inspect container (show all information of a container)
```bash
docker inspect container_name
```
### Log container (show all log, stdin, stdout history of the `initial terminal shell` of container)
Show log
```bash
docker logs container_name
```
Show log with timestamp of each command
```bash
docker logs -t container_name
```
Show log continuously
```bash
docker logs -f container_name
```

<br/>  

## Operate container
### Start container (start inactive container)
Start an inactive container (created by run command) in detach mode
```bash
docker start container_name
```
Start and jump into the initial terminal shell of an inactive container (created by run command with `-it image_name shell_name` option)
```bash
docker start -i container_name
```
### Restart container (stop then start an active container or start an inactive container)
```bash
docker restart container_name
```
### Open new terminal shell in active container
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

<br/>  

## Image management
### Show all images
```bash
docker images
```
### Remove image
```bash
docker rmi image_name
```
### Show image building history (with storage allocate of each step)
```bash
docker history image_name
```
### Save image to .tar file
```bash
docker export image_name -o file_name.tar
```
### Create image from .tar file
```bash
docker import file_name.tar image_name
```
### Download image from Docker Hub
```bash
docker pull image_name
```
### Upload image to Docker Hub
```bash
docker push image_name
```

<br/>  

## Build Dockerfile
### ENTRYPOINT and CMD (autorun command when container startup)
Using
```dockerfile
ENTRYPOINT exec arg
ENTRYPOINT ["exec", "arg"]
CMD exec arg
CMD ["exec", "arg"]
```
Running
```bash
docker run container_name
docker run container_name new_cmd
docker run --entrypoint new_entry_point container_name new_cmd
```
Working with pseudo code
```python
if exist(--entrypoint):
  entrypoint = new_entry_point
else:
  entrypoint = ENTRYPOINT

if exist(new_cmd):
  cmd = new_cmd
else:
  cmd = CMD
  
the_final_autorun_startup_command = entrypoint + " " + cmd
```
