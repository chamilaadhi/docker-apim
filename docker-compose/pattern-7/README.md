### This repository contains API Manager 2.0.0 distributed deployment with Docker compose

![alt tag](https://github.com/wso2/docker-apim/blob/master/docker-compose/patterns/design/am-2.0-pattern-7.png)

#### How to run

```docker login docker.wso2.com ```

```docker-compose up -d```

This will deploy the following,

* Mysql server (container) with apimdb, userdb, regdb
* APIM Store, Publisher, Gateway Manager, Gateway Worker/ Key manager, Traffic Manager distributed Containers
* APIM Analytics Container
* Nginx Load Balancer container and points the APIM components through the load balancer.


#### How to test

Add the following entries to the /etc/hosts
```
127.0.0.1	gateway-worker-km gateway-manager publisher store traffic-manager analytics
```
If you are using docker machine, please use the docker machine IP instead of the local machine IP.

#### How to access the environment

Publisher
```
https://publisher:9445/publisher
```

Store
```
https://store:9446/store
```

Gateway Manager
```
https://gateway-manager:9444/carbon
```

Gateway Worker/ Keymanager
```
https://gateway-worker-km:9443/carbon

https://gateway-worker-km:8243
http://gateway-worker-km:8280
```

Traffic Manager
```
https://traffic-manager:9447/carbon
```

AM Analytics
```
https://analytics:9444/carbon
```


## How to run in Docker Swarm Cluster

### Setup Docker Swarm Cluster in Amazon AWS

https://beta.docker.com/docs/aws/

### Deploy on Swarm

Change docker-compose-swarm.yml image names according to your docker private registry or public registry.

eg. If you have a docker public registry account (say account name is "lakwarus"), you can change images as following

```
docker.wso2.com/swarm-apim-pattern7-mysql:5.5				-> lakwarus/swarm-apim-pattern7-mysql:5.5
docker.wso2.com/svnrepo										-> lakwarus/svnrepo
docker.wso2.com/swarm-apim-pattern7-am-analytics:2.0.0		-> lakwarus/swarm-apim-pattern7-am-analytics:2.0.0
docker.wso2.com/swarm-apim-pattern7-traffic-manager:2.0.0	-> lakwarus/swarm-apim-pattern7-traffic-manager:2.0.0
docker.wso2.com/swarm-apim-pattern7-gateway-manager:2.0.0	-> lakwarus/swarm-apim-pattern7-gateway-manager:2.0.0
docker.wso2.com/swarm-apim-pattern7-gateway-worker-km:2.0.0	-> lakwarus/swarm-apim-pattern7-gateway-worker-km:2.0.0
docker.wso2.com/swarm-apim-pattern7-store:2.0.0				-> lakwarus/swarm-apim-pattern7-store:2.0.0
docker.wso2.com/swarm-apim-pattern7-publisher:2.0.0			-> lakwarus/swarm-apim-pattern7-publisher:2.0.0

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

Copy pattern7.dab file to docker swarm manager node and run following

To deploy all docker services on swarm cluster
```
docker deploy pattern7
```
To update AWS ELB endpoits
```
docker service update --publish-add 9448:9444 pattern7_analytics
docker service update --publish-add 9447:9443 pattern7_traffic-manager
docker service update --publish-add 9444:9443 pattern7_gateway-manager
docker service update --publish-add 9443:9443 pattern7_gateway-worker-km
docker service update --publish-add 8280:8280 pattern7_gateway-worker-km
docker service update --publish-add 8243:8243 pattern7_gateway-worker-km
docker service update --publish-add 9446:9443 pattern7_store
docker service update --publish-add 9445:9443 pattern7_publisher
```
#### How to test

Update your DNS (or add host entries) by poining following domain names,
```
analytics keymanager gateway-worker gateway-manager publisher-store traffic-manager
```
to AWS ELB IP.  

#### How to access the environment

Publisher
```
https://publisher:9445/publisher
```

Store
```
https://store:9446/store
```

Gateway Manager
```
https://gateway-manager:9444/carbon
```

Gateway Worker/ Keymanager
```
https://gateway-worker-km:9443/carbon

https://gateway-worker-km:8243
http://gateway-worker-km:8280
```

Traffic Manager
```
https://traffic-manager:9447/carbon
```

AM Analytics
```
https://analytics:9444/carbon
```
