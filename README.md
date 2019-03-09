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

## What's Currently Installed
OS: Ubuntu server 18.04 <br/>

Nvidia 410 Driver <br/>
Docker 18.09.3 <br/>
Nvidia-Docker 2.0.3 <br/>
Git 2.17.1 <br/>

[Nvidia docker](https://github.com/NVIDIA/nvidia-docker) is a docker runtime which gives docker containers access to the host machines GPU's.

## Using Docker for Deep Learning

[Docker containers](https://devops.com/docker-vs-vms/) are like lightweight VM's which are better at sharing the host machines resources.

They are ideal for deep learning as there are loads of good docker images available online to create isolated containers and provision whatever deep learning libraries and frameworks are required. It saves the hassle of manually installing things and managing dependencies on the server itself. It will help keep the server clean and and give us freedom to create loads of different environments. I'm using the *ceshine/cuda-fastai* docker image from [this guide](https://medium.com/the-artificial-impostor/docker-nvidia-gpu-nvidia-docker-808b23e1657) for now, as it has the libraries I use.

https://github.com/floydhub/dl-docker

## Data Management

When you start a docker container you pass a shared folder to it from the host machine, only the changes that are made in the shared folder will be kept when the container is stopped, everything else is destroyed. I created two folders on the root directory of the machine **/docker_shared_data/sean_data** and **/docker_shared_data/vitor_data**. So we can pass these as shared folders to the docker containers we create and back up the code from these folders using git.

## Example: Creating ceshine/cuda-fastai container and forwarding Jupyter notebook to remote machine

#### Create container from image:
```
sudo nvidia-docker run -p 8889:8888 --init -it -v /docker_shared_data/sean_data:/sharedfolder --name my_container ceshine/cuda-fastai
```

| Parameter      | Explanation |
|----------------|-------------|
|`--name my_container`             | Names this container "my_container" |
|`-it`             | This creates an interactive terminal you can use with your container |
|`-p 8889:8888`    | This exposes the ports inside the container so they can be accessed from the host. The format is `-p <host-port>:<container-port>`. The default jupyter notebook runs on port 8888 on the container so in this case we forward this to port 8889 on the host machine. |
|`-v /docker_shared_data/sean_data:/sharedfolder` | This shares the folder `/docker_shared_data/sean_data` on the host machine to `/sharedfolder` inside the container. Any data written to this folder by the container will be persistent.
|`ceshine/cuda-fastai`   | This the image that you want to run. |

This particular image will automatically launch jupyter upon run, so you need to open a second ssh window. Other ones launch bash etc.. 

#### Open terminal in container 
This opens a bash shell in the container:
```
sudo docker exec -it my_container /bin/bash
```
#### Tunnelling Jupyter to remote machine

Jupyter of the container is exposed at port 8889 on the server as specified in our run command.

**1.** Tunnel from server port to gateway port
ssh into gateway: ```ssh username@gateway.computing.dcu.ie```
On gateway run: ```ssh -N -f -L localhost:8889:localhost:8889 deepbox19@136.206.46.19```
Jupyter is now exposed on port 8889 of the gateway.

**2.** Tunnel from gateway to remote machine
On remote machine run: ```ssh -N -f -L localhost:8889:localhost:8889 username@gateway.computing.dcu.ie```
Jupyter is now exposed on port 8889 of the remote machine.
Open localhost:8889 in browser.

https://amber-md.github.io/pytraj/latest/tutorials/remote_jupyter_notebook

## Sharing the machine
#### Jupyter tunnelling  
If we happen to be using the machine at the same time and forwarding Jupyter ports through tunnel as in example above we need to use different port numbers to avoid problems.
- Sean use port **8889** to foward Jupyter
- Vitor use port **8890** to forward Jupyter

#### Sharing the CPU

## Future Changes
This is just the setup I think will work best now, we can see how this goes and change it later on if we need to.
