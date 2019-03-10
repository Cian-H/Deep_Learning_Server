# Deep Learning Server

<p align="center">
  <img src="https://github.com/squinn95/Deep_Learning_Server/blob/master/images.jpg?raw=true" />
</p>

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

**2.** From inside the school of computing gateway SSH into the machine
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

[Docker containers](https://devops.com/docker-vs-vms/) are like lightweight VM's which are better at sharing the host machines resources. Once you have created a container from a particular docker image once, containers using that same image can then be spun up instantly in future.

Docker is ideal for deep learning as there are loads of good docker images available online to create isolated containers and provision whatever deep learning libraries and frameworks are required. It saves the hassle of manually installing things and managing dependencies on the server itself. It will help keep the server clean and and give us freedom to create loads of different environments. 

## Data Management

When you start a docker container you pass a shared folder to it from the host machine, only the changes that are made in the shared folder will be kept when the container is stopped, everything else is destroyed. I created two folders on the root directory of the machine **/docker_shared_data/sean_data** and **/docker_shared_data/vitor_data**. So we can pass the shared folder to the docker containers we create and back up the code from the folder using git.

## Example: Creating container and forwarding Jupyter notebook to the remote machine

I'm going to use the *ceshine/cuda-fastai* docker image from [this guide](https://medium.com/the-artificial-impostor/docker-nvidia-gpu-nvidia-docker-808b23e1657) for now, as it has the libraries I use (PyTorch). Here's an example of runing a container from this image and tunnelling jupyter from it.

#### Create container from image:
```
sudo nvidia-docker run -p 8889:8888 --init -it -v /docker_shared_data/sean_data:/home/fastai/fastai-courses/sharedfolder --name my_container ceshine/cuda-fastai
```

| Parameter      | Explanation |
|----------------|-------------|
|`--name my_container`             | Names this container "my_container" |
|`-it`             | This creates an interactive terminal you can use with your container |
|`-p 8889:8888`    | This exposes the ports inside the container so they can be accessed from the host. The format is `-p <host-port>:<container-port>`. The default jupyter notebook runs on port 8888 on the container so in this case we forward this to port 8889 on the server. |
|`-v /docker_shared_data/sean_data:/home/sharedfolder` | This shares the folder `/docker_shared_data/sean_data` on the host machine to `/home/fastai/fastai-courses/sharedfolder` inside the container. Any data written to this folder by the container will be persistent.
|`ceshine/cuda-fastai`   | This the image that you want to run. |

This particular image will automatically launch jupyter upon run. Note the jupyter access token which comes up, you need this later for jupyter tunnelling.

#### Open terminal in container 
This opens a bash shell in the container:
```
sudo docker exec -it my_container /bin/bash
```

#### Tunnelling Jupyter to remote machine

Jupyter of the container is exposed at port 8889 on the server as specified in our run command.

**1.** Tunnel from server to gateway  <br/>
- ssh into gateway: ```ssh username@gateway.computing.dcu.ie```  <br/>
- On gateway run: ```ssh -N -f -L localhost:8889:localhost:8889 deepbox19@136.206.46.19```  <br/>
- Jupyter is now exposed on port 8889 of the gateway.  <br/>

**2.** Tunnel from gateway to remote machine  <br/>
- On remote machine run: ```ssh -N -f -L localhost:8889:localhost:8889 username@gateway.computing.dcu.ie```  <br/>
- Jupyter is now exposed on port 8889 of the remote machine.  <br/>
- Open localhost:8889 in browser. <br/>
- Copy and paste in the jupyter access token, which comes up on the terminal when you run the container.

## Sharing the machine
#### Jupyter tunnelling  
If we happen to be using the machine at the same time and forwarding Jupyter ports through tunnels as in example above we need to use different port numbers to avoid problems.
- Sean use port **8889** to foward Jupyter
- Vitor use port **8890** to forward Jupyter

#### Sharing the GPU
The machine is built to take two Nvidia Titan Xp GPU's but for the time being we only have one, hopefully nvidia will give us another free one this year.

Nvidia-docker seems to work okay for sharing GPU time between multiple docker containers, according to [here](https://github.com/NVIDIA/nvidia-docker/issues/808) anyways, so we don't need to worry about training models at the same time. Unless we're under time pressure to get something trained very quickly.

## Future Changes
This is just the setup I think will work best now, nvidia-docker seem's like the best solution currently available. We can see how this goes and change the setup in future if we need to.

## But can it handle the hardest (and most important) AI challenge of them all?

<p align="center">
  <img src="https://github.com/squinn95/Deep_Learning_Server/blob/master/1_SVeDXiWfTR5_1X2ct5THVA.jpeg?raw=true"/>
</p>

"The [chihuahua or muffin challenge](https://www.topbots.com/chihuahua-muffin-searching-best-computer-vision-api/) is beyond any learning algorithm we currently possess, it is unlikely to be solved during our lifetime" - Yann LeCunn, 2018
