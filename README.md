# Deep Learning Server

<p align="center">
  <img src="https://github.com/squinn95/Deep_Learning_Server/blob/master/images.jpg?raw=true" />
</p>

**Machine Name:** deepbox19 <br/>
**User Name:** deepbox19 <br/>
**Password:** deepbox19 <br/>
**IP Address:** 136.206.46.157 <br/>
**Location:** Lab L130, School of Computing <br/>

## SSH Access

**1.** SSH into school of computing gateway
```ssh username@gateway.computing.dcu.ie```
- The username is from your school of computing account, this is different to the dcu account.
- If you don't have a school of computing account email Gary Conway (gary.conway@dcu.ie) to get one.

**2.** From inside the school of computing gateway SSH into the machine
```ssh deepbox19@136.206.46.157```
- If you are connected via ethernet cable anywhere in the computing building you don't need to go through gateway. 

## What's Currently Installed
OS: Ubuntu server 18.04 <br/>

Nvidia Driver 440.26 <br/>
Docker 19.03.4 <br/>
nvidia-container-toolkit <br/>
Git 2.17.1 <br/>

[Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker) is a docker runtime which gives docker containers access to the host machines GPU's.

## Data Management

When you start a docker container you pass a shared folder to it from the host machine, only the changes that are made in the shared folder will be kept when the container is stopped, everything else is destroyed. I created two folders on the root directory of the machine **/docker_shared_data/sean_data** and **/docker_shared_data/vitor_data**. So we can pass the shared folder to the docker containers we create and back up the code from the folder using git.

## Example: Creating container and forwarding Jupyter notebook to the remote machine

#### Create container from image:
```
sudo docker run --shm-size 1g --gpus '"device=0"' -p 8889:8888 --init -it -v /docker_shared_data/sean_data:/home/fastai/fastai-courses/sharedfolder --name my_container ceshine/cuda-fastai
```

| Parameter      | Explanation |
|----------------|-------------|
|`--name my_container`             | Names this container "my_container" |
|`-it`             | This creates an interactive terminal you can use with your container |
|`-p 8889:8888`    | This exposes the ports inside the container so they can be accessed from the host. The format is `-p <host-port>:<container-port>`. The default jupyter notebook runs on port 8888 on the container so in this case we forward this to port 8889 on the server. |
|`-v /docker_shared_data/sean_data:/home/fastai/fastai-courses/sharedfolder` | This shares the folder `/docker_shared_data/sean_data` on the host machine to `/home/fastai/fastai-courses/sharedfolder` inside the container. Any data written to this folder by the container will be persistent.
|`ceshine/cuda-fastai`   | This the image that you want to create container from. |
|`--shm-size`   | Amount of shared RAM to pass, default is very small & sometimes causes problems with big models in PyTorch |
|`--gpus`   | Specifify which GPU(s) to allow container access to |

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
- On gateway run: ```ssh -N -f -L localhost:8889:localhost:8889 deepbox19@136.206.46.157```  <br/>
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
- Use nvidia-smi to monitor GPU usage, if we try to allocate more than 12GB at a time everything crashes
- If memory is allocated but "Volatile GPU-Util" is 0% then the script has crashed

<p align="center">
  <img src="https://github.com/squinn95/Deep_Learning_Server/blob/master/Screenshot 2019-11-15 at 12.05.42.png?raw=true"/>
</p>

## Chihuahua or muffin

<p align="center">
  <img src="https://github.com/squinn95/Deep_Learning_Server/blob/master/1_SVeDXiWfTR5_1X2ct5THVA.jpeg?raw=true"/>
</p>
