# clarus_inference_kit

This repository contains the docker-compose file needed to install & deploy the edge services related with the inference execution & monitoring at pilot's edge. Two services are provided.
 
 - Inference downloader service: This service allows the download of an inference image and the execution as a docker container.


 - Concept drift service


## Requirements
- Linux machine with  8 GB RAM and 70 GB Disk and connection to Internet 
- Ubuntu 22 or higher
- Docker version 23.0.5 or above installed
- Docker-compose version 1.29.2 or above installed
- MLOPs platform Docker registry (deployed at 34.250.205.215:5001) configured as insecure-registry in /etc/docker/daemon.json
- Docker network clarus_inference_network should exist in docker

## Deployment 
The content needed for the deployment is available in the Clarus github repository https://github.com/CLARUS-Project and the images used are in the Clarus docker hub repository https://hub.docker.com/repositories/clarusproject.
The credentials to pull images from docker hub  are saved in the Polimi repository.

### Clone repository
- Clone clarus_node_agent
    ```
    ./git clone https://github.com/CLARUS-Project/clarus_inference_kit
    ``` 

### Add docker registry in Training Platform as an insecure registry
- Move to file /etc/docker/daemon.json (create if it doesn´t exist)
- Add in the "insecure-registries" entry
  
  ```
      "insecure-registries": [
          "34.250.205.215:5001"
      ]
    ```

### Create docker network

- Type
  
  ```
      sudo docker network create --driver=bridge clarus_inference_network
    ```

### Start services
Move to the folder where the repo has been cloned.
- Login in Dockerhub using the clarusproject credentials provided in the project
    ```
    docker login -username=clarusproject
    ```


- Execute docker-compose file in the folder
    ```
    docker-compose up -d
    ```

- Once docker-compose execution is finished, the services shall be up and running. To check it, write in terminal type in terminal
    ```
    docker ps -a
    ```
You shall see next services up:

![clarus_node_agent_service](images/agent_node_deploy.png)


## Inference downloader service usage

Once the service is up and running, it provides a REST API that can be used for other processes running in the pilot plant machines. The REST API can be reached with a web browser at http://<IP_where_clarus_node_agent_is_deployed>:8081/swagger

A postman collection ClarusInference.postman.collection is provided to access the services

These are the functionalities covered by the REST API.


- [x] /api/ping. This path is for health check support
       
  ![clarus_node_agent_ping](images/rest_ping.png)



 
- [x] /api/inference/deploy (POST).
   This path will pull an inference docker image and  run it as a service in a docker container. The body request need two parameters:
    ```
    "docker_img_uri":Docker image location
    "inference_id": Unique Inference identifier. Use as container name. 
    "inference_rest_api_port": Inference image internal rest api port
    "inference_rest_api_forward_port": inference image host rest api port
    ```
    

- [x] /api/inference/deploy (DELETE).
This path will stop and remove an inference service container. The path requires the next mandatory parameter.
    ```   
    "inference_id": Inference identifier to remove. 
    
    ```

- [x] /api/inference/all (GET).
This path will provide information about the inference containers that are running in the host where the node agent is deployed. The information contains Inference ID and port where the execution service is provided.

 ![clarus_node_agent_inference](images/rest_inference_downloader.png) Update image





## Concept drift service usage
