# Liberate docker-machine setups with dind-machine

[docker-machine](https://github.com/docker/machine) is an easy to use and powerful tool. Provisioning and setting up 
cloud server with docker-machine is fast and simple. But it has one major drawback: sharing the server setup is a 
hassle due to missing configuration options and diverging versions. dind-machine is a docker in docker image 
for docker-machine which makes docker-machine portable and shareable. Provision machines with dind-machine and 
share the server configuration and server access easily with others!

## Getting Started

There are two ways you can use dind-machine. As an envirnoment inside a docker container or as shell
replacement for docker-machine. In both cases you need first to pull the docker image of dind-machine:
```
docker pull nukapi/dind-machine
```  

Now you can execute the image and use docker-machine inside it. **The option `-v ~/.dind-machine:/root/.docker/`
creates a volume mapping to the path with the docker-machine data. Do not remove it to not lose your docker-machine data! 
If you start the container without volume mapping, you will loose all certifcates docker-machine creates when you exit the container.**


```
mkdir -p ~/.dind-machine
docker run -it -v ~/.dind-machine:/root/.docker/ nukapi/dind-machine sh -l
docker-machine ls
```

To use dind-machine as docker-machine replacement you need to set an environment variable
DIND_MACHINE_DATA with the path where the docker-machine data should be stored. 

```
mkdir -p ~/.dind-machine
export DIND_MACHINE_DATA=~/.dind-machine
echo  'export DIND_MACHINE_DATA=~/.dind-machine' >> ~/.bashrc
```  
Then define an alias for dind-machine with:  
```
alias dind-machine="docker run -v $DIND_MACHINE_DATA:/root/.docker/ nukapi/dind-machine docker-machine"
echo 'alias dind-machine="docker run -i -t -v $DIND_MACHINE_DATA:/root/.docker/ nukapi/dind-machine docker-machine"' >> ~/.bashrc
```  

Now you can use docker-machine commands with dind-machine by replacing docker-machine with `dind-machine`. 
For example 
```
dind-machine ls
```
Take a look in into the [reference](https://docs.docker.com/machine/reference/)

## Connecting to the remote docker daemon
You can conveniently connect to your servers docker daemon from your local machine and initialize a 
[docker swarm](https://docs.docker.com/engine/swarm/).
:
```
eval $(dind-machine env myserver --shell zsh) && export DOCKER_CERT_PATH="$DIND_MACHINE_DATA/machine/machines/myserver"

sudo -E docker swarm init
sudo -E docker node ls
```
## Take care of your data!
Backup the dind-machine setup by backing up the folder defined by DIND_MACHINE_DATA. 
You can also share your machine setup by sharing this folder.
