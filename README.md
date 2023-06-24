# docker

// all containers 
docker ps -a
// all images 
docker images 

// run container called my-website and on port 8000 
docker run -d -name my-website 8000:80 nginx

// start exit container again
docker start containerName 

// creaye image called myredis and tag 1.1.1
docker run tag redis ahmedabdo1/myredis:1.1.1

// build image from docker file
docker build -t <image-name> .
// build image with tag
docker build -t my-nest-app:v1.0 .

// browse container 
docker exec -it my_container bash

// delete all images docker rmi -f $(docker images -aq)

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
  docker run  --name appVolumeCountainer -p 3000:3000  -d  -v feedback:/app/feedback app:volum
  this commaned run a countainer calle appVolumeCountainer  based on image app:volum 
   and create volume  called feedback  for folder /app/feedback
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

  
  
