### This repository contains API Manager 2.0.0 distributed deployment with Docker compose

![alt tag](https://github.com/wso2/docker-apim/blob/master/docker-compose/patterns/design/am-2.0-pattern-1.png)

## Pre-requisites

 * Docker 
 * Docker compose

#### Docker installation for linux
```
wget -qO- https://get.docker.com/ | sh
```

#### Docker installation for Mac

https://docs.docker.com/docker-for-mac/

#### Docker installation for Windows

https://docs.docker.com/docker-for-windows/

#### Docker Compose Installation

https://docs.docker.com/compose/install/


#### How to run

```docker login docker.wso2.com ```

```docker-compose pull```

```docker-compose up --build -d ```

This will deploy the following,

* Mysql server (container) with apimdb, userdb, regdb
* APIM Container
* Nginx Load Balancer container and points the APIM components through the load balancer.


#### How to test

Add the following entries to the /etc/hosts
```
127.0.0.1 api-manager
```
If you are using docker machine, please use the docker machine IP instead of the local maGchine IP.

#### How to access the environment

Publisher

```
https://api-manager/publisher
```

Store

```
https://api-manager/store/
```


Gateway Manager

```
https://api-manager/carbon/
```

## How to run in Docker Swarm Cluster

### Setup Docker Swarm Cluster in Amazon AWS

https://beta.docker.com/docs/aws/

### Deploy on Swarm

Change docker-compose-swarm.yml image names according to your docker private registry or public registry.

eg. If you have a docker public registry account (say account name is "lakwarus"), you can change images as following

```
docker.wso2.com/swarm-apim-pattern1-wso2am:2.0.0	-> lakwarus/swarm-apim-pattern1-wso2am:2.0.0
docker.wso2.com/swarm-apim-pattern1-mysql:5.5		-> lakwarus/swarm-apim-pattern1-mysql:5.5
```
To build all docker images
```
docker-compose -f docker-compose-swarm.yml build
```

To push newly built images to relevant docker registry
```
docker-compose -f docker-compose-swarm.yml push
```

To create bundle file

```
docker-compose -f docker-compose-swarm.yml bundle
```

Copy pattern1.dab file to docker swarm manager node and run following

To deploy all docker services on swarm cluster
```
docker deploy pattern1
```
To update AWS ELB endpoits
```
docker service update --publish-add 443:9443 pattern1_api-manager
docker service update --publish-add 80:9763 pattern1_api-manager
docker service update --publish-add 8280:8280 pattern1_api-manager
docker service update --publish-add 8243:8243 pattern1_api-manager
```
#### How to access the environment
Update your DNS (or add host entries) by poining "api-manager" domain name to AWS ELB IP.  

Publisher

```
https://api-manager/publisher
```
Store

```
https://api-manager/store/
```

Gateway Manager

```
https://api-manager/carbon/
```

