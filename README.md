# Deep Learning Server

<p align="center">
  <img src="https://github.com/squinn95/Deep_Learning_Server/blob/master/train.jpg?raw=true" />
</p>

**Machine Name:** deepbox19 <br/>
**User Name:** deepbox19 <br/>
**Password:** deepbox19 <br/>
**IP Address:** 136.206.46.7 <br/>
**Location:** Lab L130, School of Computing <br/>

## SSH Access

**1.** SSH into appropriate school of computing gateway
```ssh username@student.computing.dcu.ie```
- The username is from your school of computing account, this is different to the dcu account.
- If you don't have a school of computing account email Gary Conway (gary.conway@dcu.ie) to get one.
- There are several gateways, student.computing might not be the correct gateway for you, if not, email Gary Conway to find out correct one.

**2.** From inside the school of computing gateway SSH into the machine
```ssh deepbox19@136.206.46.7```
- If you are connected via ethernet cable anywhere in the computing building you don't need to go through gateway. 

## GPU's Installed
[Nvidia Titan Xp](https://www.nvidia.com/en-us/titan/titan-xp/) (12 GB) <br/>
[Nvidia Titan RTX](https://www.nvidia.com/en-us/deep-learning-ai/products/titan-rtx/) (24 GB)

## Software Installed
Ubuntu Server <br/>
Nvidia Driver <br/>
Docker <br/>
nvidia-container-toolkit <br/>
Git <br/>

[Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker) is a docker runtime which gives docker containers access to the host machines GPU's.

## Sharing the machine

#### Time Allocation

- We have a [Google calendar](https://calendar.google.com/calendar/embed?src=c_9p6hfag258rpqilmo8cb2sko34%40group.calendar.google.com&ctz=Europe%2FDublin) to coordinate time scheduling for the servers GPU's, ask someone to be added to it.
- Mark off time slots on the calendar with your name and which GPU you will be using. If you are only using a % of the GPU's memory, include this info.

#### Port Tunnelling
We need to keep a record of what port numbers are in use for port tunnelling to avoid problems/confusion. Due to the new security setup in School of Computing we also need to let the school admins (gary.conway@dcu.ie) know which ports are being used (and what for) so custom rules can be written on their side to allow it.

List of ports notified to school admins:
- Seán, **8889**, Jupyter Notebook
- Vitor, **7474**, Neo4j
- Vitor, **8080**, Apache Airflow

## Example: Creating container and forwarding Jupyter notebook to the remote machine

#### Create container from image:
```
sudo docker run --shm-size 1g --gpus '"device=0"' -p 1234:8888 --init -it -v /docker_shared_data/dummy:/home/fastai/fastai-courses/sharedfolder --name my_container ceshine/cuda-fastai
```

| Parameter      | Explanation |
|----------------|-------------|
|`--name my_container`             | Names this container "my_container" |
|`-it`             | This creates an interactive terminal you can use with your container |
|`-p 1234:8888`    | This exposes the ports inside the container so they can be accessed from the host. The format is `-p <host-port>:<container-port>`. The default jupyter notebook runs on port 8888 on the container so in this case we forward this to port 1234 on the server. |
|`-v /docker_shared_data/dummy:/home/fastai/fastai-courses/sharedfolder` | This shares the folder `/docker_shared_data/dummy` on the host machine to `/home/fastai/fastai-courses/sharedfolder` inside the container. Any data written to this folder by the container will be persistent.
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

Jupyter of the container is exposed at port 1234 on the server as specified in our run command.

**1.** Tunnel from server to gateway  <br/>
- ssh into gateway: ```ssh username@student.computing.dcu.ie```  <br/>
- On gateway run: ```ssh -N -f -L localhost:1234:localhost:1234 deepbox19@136.206.46.7```  <br/>
- Jupyter is now exposed on port 1234 of the gateway.  <br/>

**2.** Tunnel from gateway to remote machine  <br/>
- On remote machine run: ```ssh -N -f -L localhost:1234:localhost:1234 username@student.computing.dcu.ie```  <br/>
- Jupyter is now exposed on port 1234 of the remote machine.  <br/>
- Open localhost:1234 in browser. <br/>
- Copy and paste in the jupyter access token, which comes up on the terminal when you run the container.
