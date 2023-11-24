# docker

// all containers 
docker ps -a
// all images 
docker images 

// run container called my-website and on port 8000 
docker run -d -name my-website 8000:80 nginx

// start exit container again
docker start containerName 

// creaye container called myredis and tag 1.1.1
docker run tag redis ahmedabdo1/myredis:1.1.1

// build image from docker file
docker build -t <image-name> .
// build image with tag
docker build -t my-nest-app:v1.0 .

// browse container 
docker exec -it my_container bash

// delete all images 
docker rmi -f $(docker images -aq)

//pull inage 

pull image 

//delete image 
docker rmi <image_name>

// delete all containers
docker rm -f $(docker ps -aq)
  
  notes 
 // 1- EXPOSE in docekr file for only docmentation optional
  
 // 2-  attach mode you can lisen to logs for container 
      dettach mode countainer running in the backgroun 
       defualt behouvior for "docker start countainer_name"  is dettach 
       to make it in attach mode  "docker logs -f countainer_name"
  
//3- auto delete countainer after stop 
    "docker run --rm image_name"
  
//4- docker image inspect image_name
  
5- docker tag old_image_name new_image_name
  
6- to explore the running container 
   docker exec  -it container_name sh
  
7- docker volumes
    1- anonumous volume manged by docker and deleted when we delete container
    2- named volume manged by docker and nit deleted ...... 
    3- bind mounted connect docker container files with our localhost files and be carefull 
       to use it becouse may container files overwrite your localhost  files  
       ex: -v  "$(pwd):/app" 
  docker run  --name appVolumeCountainer -p 3000:3000  -d  -v feedback:/app/feedback app:volum
  this commaned run a countainer calle appVolumeCountainer  based on image app:volum 
   and create volume  called feedback  for folder /app/feedback

  bind mount for your Node.js application but exclude the node_modules
    docker run -d --name node_app -v "$(pwd):/app" -v /app/node_modules my_node_app
    "-v "$(pwd):/app" binds your current directory (excluding node_modules) to the /app directory in the container.
    "-v /app/node_modules" creates an anonymous volume at the /app/node_modules directory within the container,
    effectively excluding the node_modules directory from the bind mount.

8- anonymous volume (without name ) will remove with container not used for perisit data
  
 9- bind mount volume can overwrite localhost files (your codes) so ypu should add :ro
  
   ex: docker run  -v "home/folder/project:path to container:ro"
  
 10- --env  to set variable ex
   in code : app.listen(process.env.PORT);
   dockerFile:
    ENV PORT 3000  // defoult value if we did not specify the value in docker comand 
    EXPOSE $PORT 
   docker command: docker run -p 3000:4000    --env PORT=4000  image_name
  
 11 - --env-file we can put PORT=4000 in .env file 
  ex: make .env file and  write on it PORT=4000 
  docke rcomand : docker run -p 3000:4000    --env-file ./.env  image_name 
  
  12- ARG  if you want to set defualt  port dinamiclly in building the image 
      ex: dockerfile 
        ARG DEFAULT_PORT=3000
        ENV PORT $DEFAULT_PORT
      docker comannd docker build -t  image:arg --build-arg DEFAULT_PORT=3000 .
  
  13- network 
      container can send http request to web without any config
      to connect to your local host from a container use host.docker.internal 
      ex : 'mongodb://host.docker.internal :27017/swfavorites',
      
  to connect to network 
   first create network : docker network create my-net 
   secound  : docker run --network my-net  --name container1 image 
   third : docker run --network my-net  --name container2   image 
   
  14- react app container must run with -it   
   docker run  -it  reactimage 
   react: runs in the browser so it cant join the docker network or connect to container in docker network
  
  15- to make container act as it running in local host you have to use  -p 3000:3000 or any port 
      for example front end cant connect docker network and it will only connect to connect to local host nestwork 

   note : if two containers in the same network it can comunicate each other uing other container name 
   ex i have container1  , container2 (is the mongodb container) , network:my-net 
     inside container1 i can connect container2 by :
      

    mongoose.connect(
      'mongodb://container2:27017/course-goals',
      {
        useNewUrlParser: true,
        useUnifiedTopology: true,
      },
      (err) => {
        if (err) {
          console.error('FAILED TO CONNECT TO MONGODB');
          console.error(err);
        } else {
          console.log('CONNECTED TO MONGODB');
          app.listen(8000);
        }
      }
    );

16 docker compose 



version: "3.8"  // version of comanneds
services: // name of servces or containers that will running 
  mongodb:  // service 1 
    image: 'mongo' // use image mogo from docker hub 
  # container_name: monogahmedcontainer  //optional to name your container if it not spicfy that i will randomly generated
    volumes: 
      - data:/data/db //name volume it must to be added in volumes below
    # environment:  // two ways to add envs specify one by one or 
    #   MONGO_INITDB_ROOT_USERNAME: max
    #   MONGO_INITDB_ROOT_PASSWORD: secret
      # - MONGO_INITDB_ROOT_USERNAME=max
    env_file:   //  add  env file path
      - ./env/mongo.env
  backend:
    build: ./backend // use docker file to build the image 
    # build:  // optinal if you named docker file with any other name you must specify the context and deockerfile : 
    #   context: ./backend  
    #   dockerfile: Dockerfile
    #   args:
    #     some-arg: 1
    ports:
      - '80:80'
    volumes: 
      - logs:/app/logs // named container must add to volumes below
      - ./backend:/app // it is the bind mount no need to write the abslute path
      - /app/node_modules // anynoumous conatiner name 
    env_file: 
      - ./env/backend.env
    depends_on:
      - mongodb
  frontend:
    build: ./frontend
    ports: 
      - '3000:3000'
    volumes: 
      - ./frontend/src:/app/src
    stdin_open: true // it is to use -it  to run react container 
    tty: true
    depends_on:  // not running untill run backend container 
      - backend

volumes: 
  data:
  logs:

  #  docker-compose up --build

  
  
17- deploy instance for aws 
    after runnung ec2 instance and download secreats .pem
    coneect to ec2 using bash terminal 
    then install docker 
    * sudo yum -y update
    *sudo amazon-linux-extras install docker
    *sudo service docker start
    *sudo chkconfig docker on
  
  18-ECS elastic container service it is third party service that mange container for you
  In AWS, when you run containers using services like Amazon ECS (Elastic Container Service) 
   or EKS (Elastic Kubernetes Service), the IP addresses of the containers can also change 
   due to the dynamic nature of container orchestration.


19- Multi-stage builds in Docker are a feature that allows you to use multiple FROM statements 
in your Dockerfile. Each FROM instruction can have its own environment and context, and only the
final stage is retained in the final Docker image. 
ex: React app require multi stage build one for build and other for run :


# Stage 1: Build React App
FROM node:14 as build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Create a lightweight image to serve the built app
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

note : you can excute only one stage using --target flag 
docker build --target build -t my-react-app-build .

Kubernetes


Kubernetes offers a range of benefits for both developers and operations teams. Here are some key ways in which Kubernetes can benefit you:

1-Container Orchestration: Kubernetes automates the deployment, scaling, and management of containerized applications.
It allows you to define how your applications should run, scale, and interact with each other.

2-Scalability: Kubernetes can automatically scale your application based on demand. It can scale the number of container
instances up or down to handle varying workloads, ensuring that your application remains available and responsive.

3-Service Discovery and Load Balancing: Kubernetes provides built-in service discovery and load balancing. It assigns
a unique IP address and DNS name to each service, allowing other services to discover and communicate with them. Load
balancing ensures that traffic is distributed evenly across multiple instances of a service.

4-Rolling Updates and Rollbacks: Kubernetes supports rolling updates, allowing you to update your application without 
downtime. If an update causes issues, you can easily roll back to a previous version.

5-Declarative Configuration: You describe the desired state of your applications and infrastructure in configuration
files (usually in YAML). Kubernetes then takes care of bringing the actual state in line with the desired state, simplifying deployment and management.

6-Resource Utilization: Kubernetes optimizes resource utilization by packing containers onto nodes based on their resource
requirements. This helps in efficient use of compute resources and cost savings.

7-Multi-Cloud and Hybrid Cloud Support: Kubernetes is designed to work across various cloud providers and on-premises 
data centers. This provides flexibility and avoids vendor lock-in, allowing you to run your applications wherever
it makes the most sense for your organization.

8-Portability and Consistency: Kubernetes provides a consistent environment for deploying and running applications,
regardless of the underlying infrastructure. This ensures that applications behave consistently in development,
testing, and production environments.

9-Health Checks and Self-healing: Kubernetes continuously monitors the health of your applications and automatically 
restarts or replaces failed containers. This self-healing capability improves the overall resilience of your applications.

10-Ecosystem and Extensibility: Kubernetes has a vibrant ecosystem with a wide range of tools, services, and plugins. 
It's extensible, allowing you to integrate with other tools and services to meet your specific requirements.


----- architecture composed 


Master Node:

API Server: Exposes the Kubernetes API and is the entry point for
all administrative tasks.
Controller Manager: Ensures the desired state of the cluster by managing 
controllers (background processes that regulate the state of the system).
Scheduler: Assigns workloads to nodes based on resource requirements, constraints, and other policies.



Node (or Minion):

Kubelet: Ensures that containers are running on the node as expected. It communicates
with the API server to receive instructions for maintaining the desired state.
Container Runtime: The software responsible for running containers, such as Docker or containerd.
Kube Proxy: Maintains network rules on nodes, enabling communication between different pods and external traffic.

Pods:

The smallest deployable units in Kubernetes. Pods contain one or more 
containers that share the same network namespace and storage.




what is kubectl ?
is a command-line tool that allows you to interact with Kubernetes clusters. 
it enables users to perform various tasks such as deploying applications,
inspecting and managing cluster resources, and troubleshooting issues

what is minikube?
Minikube is a tool that allows you to run Kubernetes clusters locally on your machine.
It's designed to enable developers to quickly set up a single-node Kubernetes cluster for
testing and development purposes. Minikube is particularly useful for those who want to learn
and experiment with Kubernetes without needing access to a full-scale cluster.


what do you need to install Kubernetes?

you need to inatall kubectl and minikube
then :  minikube start --driver=docker with adminstaration termenal
minikube  status 
minikube dashboard

to create deployment 
first you need to build the image you want to deploy (ahmeddbah/kube-first-test) 
ex: docker build -t ahmeddbah/kube-first-test:3 .  // you have to set tag version
second push it to docker hub 
then: kubectl create deployment --image=ahmeddbah/kube-first-test
to verify the deployment  kubectl get deployment  and kubectl get pods
to change the name of image: docker tag old-image-name:old-tag new-image-name:new-tag


objects of Kubernetes 
objects are the basic building blocks that represent the state of your cluster. 
1-Service:
An abstraction that defines a logical set of pods and a policy by which to 
access them. Services enable network communication between different sets of pods.
2-Deployment:
Manages ReplicaSets and provides declarative
updates to applications. Deployments allow you to describe the
desired state of your application and automatically handle the rollout of changes.
3-Pod:
The smallest deployable unit in Kubernetes. 
A pod represents a single instance of a running process in a 
cluster. It can contain one or more containers that share the same network namespace and storage.

create service 

1-docker expose deployment first-ap-kube --type=LoadBalancer --port=8080   , --type=NodePort // it will accessible but without load balancer 
first-ap-kube deployment must be exist 
2- minikube service first-ap-kube (deployment name )  //This will create the load balancer endpoint 

restart pod : 
 the Kubernetes will start the pods if they fail or stop 
 
 scale pods :
  we can scale our app by making more replicas so the load balancer will distribute the requests among them 
 kubectl scale deployment first-ap-kube  --replicas=3 // it will create 3 replica
  
update container on the deployment :
 kubectl set image deployment/first-ap-kube  kube-first-test=ahmeddbah/kube-first-test:2
It looks like you are using the kubectl command to update the image of a deployment in Kubernetes. 
The command you provided is correct for updating the image of a deployment named first-ap-kube and 
the container within it named kube-first-test to version 2 of the ahmeddbah/kube-first-test image.

declarative way to create kubernetes 
go and make file with .yaml
ex:deployment.yaml 

 apiVersion: app/v1
kind: Deployment  # other values is service ... you can find it in docs 
metadata: 
  name: deployment-test
spec: 
  replicas: 2
  selector:
    matchLabels:
      app: dbah-app
  template:  # for pod and image of pad
    metadata:
      labels:
        app: dbah-app # you can name it anything ex deploy:dbah-app
    spec:
      containers:
        - name: dbah-node
          image: ahmeddbah/kube-first-test:3
    
  






  


