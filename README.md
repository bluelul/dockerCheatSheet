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
- ***If fail, Log out and log back in*** so that your group membership is re-evaluated.

## Build Hello World image
