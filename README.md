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
  
  
  
