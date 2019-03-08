# Deep Learning Server
**Machine Name:** deepbox19 <br/>
**User Name:** deepbox19 <br/>
**Password:** deepbox19 <br/>
**IP Address:** 136.206.46.19 <br/>
**Location:** Lab L130, School of Computing <br/>

## SSH Access

**1.** SSH into school of computing gateway
```ssh username@gateway.computing.dcu.ie```
- The username is from your school of computing account, this is different to the dcu account.
- If you don't have a school of computing account email Gary Conway (gary.conway@dcu.ie) to get one.

**2.** From inside school of computing LAN SSH into the machine
```ssh deepbox19@136.206.46.19```
- If you are connected via ethernet cable anywhere in the computing building you don't need to go through gateway. 

## Whats Currently Installed
OS: Ubuntu server 18.04 <br/>

Nvidia 410 Driver <br/>
Docker 18.09.3 <br/>
Nvidia-Docker 2.0.3 <br/>
Git 2.17.1 <br/>

[Nvidia docker](https://github.com/NVIDIA/nvidia-docker) is a docker runtime which gives docker containers access to the host machines GPU's.

## Using Docker for Deep Learning

Docker containers are ideal for deep learning as there are loads of docker images available online to provision whatever libraries and frameworks are required to a container. It saves the hassle of manually installing things and managing dependencies. It will also help keep the server clean and and give us freedom to create loads of different environments. I'm using the *ceshine/cuda-fastai* docker image from [this guide](https://medium.com/the-artificial-impostor/docker-nvidia-gpu-nvidia-docker-808b23e1657) for now, as it has the libraries I use.

https://github.com/floydhub/dl-docker

## Data Management

When you start a docker container you pass a shared folder to it from the host machine, only the changes that are made in the shared folder will be kept when the container is stopped, everything else is destroyed. I created two folders on the root directory of the machine **/docker_shared_data/sean_data** and **/docker_shared_data/vitor_data**. So we pass can pass these as shared folders to docker containers.

## Forwarding Python Notebooks

https://amber-md.github.io/pytraj/latest/tutorials/remote_jupyter_notebook
