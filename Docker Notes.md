###### **Docker Introduction:**



* **Docker is an opensource platform where we can develop and test and ship and deploy the applications inside an container.**
* **Container is an Light weight package that contains the application code, runtime environment, libraries, dependencies and configuration files.**



###### **Properties:**



1. **Portability: It provides the portability of the applications by sharing the container through docker image.**
2. **Light weight: It is less Overhead, meaning easy to create, update, modify and  delete the containers. And also it is storage is less compared to the Virtual Machine. We can build multiple applications parallelly in one single container. We can use the same dependencies with different versions for different applications.**



###### **Docker Images:**



* **This is an .exe file which has instructions to build an container. Docker image is used to build an container.**
* **Docker image is an blueprint that helps in creating the containers.**
* **We will share the this docker image with the team member to execute the containers in the others system.**

**Example for understanding: Running ubuntu completely with isolation \[any changes done using ubuntu doesn't effect the MacOS] in MacOS using docker.**

**command: docker run -it ubuntu  \[-it means interactive mode]**

* **When docker is compared with Virtual Machine it seems similar but Docker is light weight than virtual machine.**
* **Docker download: --> sources: 1. Docker official website, 2. Microsoft Store.**



###### **Basic Docker commands:**



1. **docker pull IMAGE\_NAME : used to pull the docker image from the docker hub.**
2. **docker pull image\_name:version: This command is used to pull the specific version of the docker image.**
3. **docker images: this command is used to check the number of docker images present in the docker.**
4. **docker run IMAGE\_NAME: Used to build the container from the docker image. For container docker automatically initializes some random name Note: when we run the above command. The docker daemon will pull the image from the Docker hub. Docker daemon is the core part of the docker desktop which serves as the core engine for the docker desktop. Daemon will create the container which run on the executable.\[Every time we run docker run command we create new container every time.] \[important note: when we pull MySQL image we need to run it using setting up the password using -e MYSQL\_ROOT\_PASSWORD=sompassword, applicable for all databases];**
5. **docker run -it IMAGE\_NAME: This command executes the image in interactive mode, which means it allows to access container terminal which helps us in input or output.**
6. **docker run -d image\_name: This command is used to run the image and execute the container in the background.**
7. **docker ps -a: This command is used to check the number of containers created in the docker and their properties.\[-a refers to all in the command so we will get all the container created].**
8. **docker ps: This command is used to list the actively running containers in running state.**
9. **docker start container\_name or container\_id: This command is used to start the existing container.**
10. **docker stop container\_name or container\_id: This command is used to stop the running existing container.**
11. **docker rmi Image-name: This command is used to remove the image from the docker.**
12. **docker rm container\_name: This command is used to remove the container.**
13. **docker run --name custom\_name -d image\_name: This command is used to create the container with custom name when we run the image.**

    1. &#x20;**docker image layers:**

&#x20;              				**container**

&#x09;				    **|**

&#x09;				 **Layer 2**

&#x09;				    **|**

&#x09;				 **Layer 1**

&#x09;				    **|**

&#x09;				**Base Layer**



**14. Docker image is made up of different layers. docker layer is collection of different layers.**

**15. Base layer: it is the small size Linux based image(debanin, alphine, etc). These layers are immutable and read only layers that supports only read only layers.**

**16. Upon the base layer we can have number of layers and ono the top we have container layer on the top of underlying layers.**

**17. Assume there are 2 same images created with different versions. These images will be created using different layers. In these images there will be some layers in common because the same image is installed with different version so there will be common in some layers. So while installing different version for example think of installing MySQL images with newer version and another with 8.x version. So when there will be some common layers in the**

**newer version and 9.x version so at that layers it shows already exists.**





###### **Port Binding:**

1. docker run -p8080:3306 IMAGE\_NAME: When the container is created by default the container is binded to an port. Container has separate virtual file system that has separate files compared to local file system. When the container is running on 3306 . If we want we can bind the 3306 with the host port for example 8080 if we want. This is called port binding. Hosting machine port ------------> with the container port. We cannot bind the one host port to many container ports. we need to bind it with different host machine ports.



###### **Troubleshoot Commands:**



1. **docker logs container\_id: This command is used to check the logs of the container using container id or name to identify the problems.**
2. **docker exec -it container\_id /bin/bash: this command is used to execute the commands int the bash of the already running container. It is no sure that every container has bash so we use /bin/bash to access the bash of the container and execute the additional commands. For example we can check the environmental variables and we can check the dependencies etc etc.**
3. **docker exec -it container\_id /bin/sh: this alternative of the above command to execte commands in the shell rather than bash.**

###### **Difference Between Docker and Virtual Machine:**

1. **Think of there are three layers in every machine . Hardware --> Host OS Kernel --->Application layer. Now the fundamental and  major difference is Docker virtualizes only application layer. Where as Virtual machine virtualizes both host kernel os and application layer.**
2. **Docker is light weight and faster and smaller in size then virtual machine.**
3. **The basic disadvantage of the docker when compared to virtual machine is the virtual machine is compactable with all the underlying operating systems because it uses it own kernal.**
4. **Docker is mainly built for Linux  based systems.**
5. **Docker desktop adds an hypervisor layer which internally uses light weight Linux distribution where we can run our container on the non-Linux based system also. Docker runs faster in Linux based systems.**



###### **Docker Network:**



* **docker has the ability to create isolated networks. Think of 2 containers need to be interacted like mongodb and mongodb express we need to interact mongodb express with mongodb we use docker network.**



###### **Use cases:**

###### **Developing application with docker:**



1. **Setting up mongo and mongo-express**:
2. **we should setup in detached mode**
3. **docker pull mongo: This command is used to install mongo in the docker.**
4. **docker run -d -p 27017:27017 --name mongo --network mongo-network -e MONGO\_INITDB\_ROOT\_USERNAME=root -e MONGO\_INITDB\_ROOT\_PASSWORD=qwerty mongo: this command is used to run the image in detached mode and port binded to the 27017 as provided in the documentation and container is creates as name mongo and to run in the network names mongo- network and the username and password are set through the -e environmental variables.**
5. **docker pull mongo-express:1.0.2-20-alpine3.19: This command is used to pull the mongo-express into the docker.**
6. **docker run -d -p 8081:8081 --name mongo-express --network mongo-network -e ME\_CONFIG\_MONGODB\_ADMINUSERNAME=root -e ME\_CONFIG\_MONGODB\_ADMINPASSWORD=qwerty -e ME\_CONFIG\_MONGODB\_URL="mongodb://root:qwerty@mongo:27017" mongo-express:1.0.2-20-alpine3.19 : this command is used to create the container and run in the -d detached mode and with the name mongo-express and the network  mongo-network and the user name and password and URL are set through the environmental variables.**

###### **Docker Compose:**

1. **Docker compose is a tool for defining and running multi-container applications.**
2. **We can run the multiple docker commands in better way for running an multi-container project. This is done by creating <name>.yaml file. Basically this file consists of multiple docker commands instead of running command one by one it executes the commands at once by executing the file at once. yaml stands for yet another markup language.**
3. **we have two benefits in doing this way**

   1. **we will be executing the commands in structed and standardized way.**
   2. **we can edit the files easily.**
4. **we will run this .yaml file using docker compose.**
5. **Indentation is very important in yaml file.**
6. **we can define environmental variables in 2 ways one first by keeping - and assigning with = assignment operator \[- MONGO\_INITDB\_ROOT\_USERNAME=admin], or by using semicolon \[MONGO\_INITDB\_ROOT\_USERNAME:admin]**
7. **When we run the docker yaml file we doesn't need to create an network. the docker will run the container the number of containers mentioned in the yaml file in an default network.**



###### **what we do in compose.yaml file:**



**--------------------------------------------------------------------------------------------------------**

**version:"3.8"**



**services:**

&#x20;   **mongo:**

&#x20;       **image: mongo**

&#x20;       **ports:**

&#x09; **-27017:27017**

&#x20;       **environment:**

&#x20;           **MONGO\_INITDB\_ROOT\_USERNAME:admin**

&#x20;           **MONGO\_INITDB\_ROOT\_PASSWORD:qwerty**

**--------------------------------------------------------------------------------------------------------**



**Main commands of docker compose:**



1. **docker compose -f filename.yaml up -d:In this command -f is the option of the file. the container in the yaml file will be created and started using the up command and -d refers for detached mode to run in the background.**
2. **docker compose -f filename.yaml down: We use this command to remove or delete the containers in the yaml file permanently we use the down.**



###### **Dockerizing our Application:**



* **The process of converting our own application into running docker container or multiple containers is called dockerizing. \[TestApplication ---> Docker image ---> container]**
* **Once our app is dockerized. This is basically the entity which we can share with our team or if we want we can deploy owerselfs.**
* **This process is done using dockerfile. In this docker file we will be having the instructions about how do we dockerize the application**
* 

**Example docker file:**

**--------------------------------------------------------------------------------------------------------**



**FROM python:3.13**

**WORKDIR /usr/local/app**



**# Install the application dependencies**

**COPY requirements.txt ./**

**RUN pip install --no-cache-dir -r requirements.txt**



**# Copy in the source code**

**COPY src ./src**

**EXPOSE 8080**



**# Setup an app user so the container doesn't run as the root user**

**RUN useradd app**

**USER app**



**CMD \["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]**



**--------------------------------------------------------------------------------------------------------**



###### **Important docker file instrucions:**

1. **FROM: generally every docker image which we create, that is based on the base image. When we use FROM we declare our base image. So what ever the base image we want we can declare it using the FROM instruction.**

   1. **Base image : let us think we need to run node.js application to run it first we need to have the node application image that is \[node] image in the docker. upon that node base image we can create our test application image on that image.**
2. **WORKDIR: We can define our working directory or this is the path where files will be copied and commands will be executed. The rest of the other instructions like copy, run, cmd, expose, env are executed in this workdir.**
3. **COPY: We use this command to copy data form the host --> image. first we definie the path of the host and then image path.**
4. **RUN: this command is used to execute the instructions in the docker image or docker container. When we define a docker file there can be multiple commands in it. This instruction will tell the builder to run the specified command.**
5. **CMD: cmd command is similar to the run command. In our defined docker file there can be multiple run commands, but only one CMD command. We can say that the CMD command is used to execute and run the application by executing the CMD command after setting up and running the container. This instruction will set the default command a container using this image will run.**
6. **EXPOSE <port number>: By using this instruction we can expose the port of the image.** 
7. **ENV: Using this instruction we can define the environmental variables.** 
8. **Note!! we should create the docker file inside the application only.** 



**Example: think of an we are creating an image called test\_app of node.js application for dockerizing the application and creating an inamge for it and running it. So to create a test\_app first we need to have some base image setup like for node.js application we need to have base image called node in our system.**



&#x09;					**testapp   ----> user own image created for node.js application**

&#x09;					   **|**

&#x09;					  **Node    ---->base image. even node also have another base image called debain. debain is an Linux based image.**

&#x09;					   **|**

&#x09;					**Debian    ----> base image of node** 



**Example docker file need to be created in the application** 

**--------------------------------------------------------------------------------------------------------**

**FROM node  # here node is an image.**



**ENV** **MONGO\_INITDB\_ROOT\_USERNAME:admin \\**

&#x20;   **MONGO\_INITDB\_ROOT\_PASSWORD:qwerty**



**RUN mkdir -p testapp**



**COPY . /testapp**



**CMD \["node","/testapp/server.js"]**

**--------------------------------------------------------------------------------------------------------**



* **To run this created testapp image we need to the execute the command docker build -t testapp:1.0**



**Docker file commands:**

* **docker build -t <name for docker image>:<tag> . \[this command is used to run the created docker image inside the application. this command detects the docker image automatically and runs it. -t refers for the tag which we want to define]. To run the container we will use docker run <imagename> and image name is replaced by the created image name to run the container. Even we can run the created container in interactive mode using the -it tag in the docker run -t <imagename that we created>.**
* **To check the application copied into our container or not we will go into the bash by running the container \[docker run -it <image created> bash] and check weather the application files are copied into the container or not.**



###### **How to publish our docker image in the docker image:**

* **to make the image available to the public or private we need to create the repository in the docker hub and we need to push in to the repository.**
* **docker build -t <user/repositoryname> . : This command is used to build the docker image in the docker hub. we will execute this command in the terminal from the application path. For example if i am having my node.js application in testapp directory we will open terminal in that directory and detects the docker file and build the image.**
* **docker login: Using this we will login in to out docker hub account to push the built docker image.**
* **docker push <user/imagename>: This is used to push the built image into the docker hub account.**



###### **Docker volume:**

* **Volumes are the persistent data stores of the containers.**
* **Generally when we stop the running container the data in the container will be lost. so to persist the data in the container we use something called volumes in docker.**
* **The extra space in the host sysem is reserved and allocated to the containers. This is done by mounting the space to the container. So the data stored in the container will be replicated to the allocated data. SO when the container is stoped and restarted again the data will be fetched from the mounted space.**
* **generally this space is mostly managed by docker or host machine operating system.**
* **The speciality about the docker volume is we can map the mounted space to the multiple container in the docker. For example container1 and container2 are maped to single data unit in the host.**
* **docker run -it -v /host/path:/container/storage/path <image name>: We use -v flag to allocate volume and first we define the path for the space in the host and then path for tha space in the host machine.**
* **docker run -m type = volume, src =<volume\_name>.dst=<mnt\_path>: this is same as the volume. and used to mount the storage.**
* **docker volume ls: This command is used to display all the volumes in the docker.**
* **docker volume create vol\_name: This is used to create the voulume with custom name. Also called named volume.By default docker creates the volumes in the root system path. \[C:\\ProgramData\\docker\\volumes] in mac or linux \[/var/lib/docker/volumes].**
* **docker volume create vol\_name: this is used to delete or remove the volumes in the docker.**
* **docker volume prune: If the volumes in the docker are not mounted to any container to delete them we use this command**
* **-v is the short form of --volume we can use either of them.**
* **Mount is also same as the volume . we use --mount or the shortform -m to define the volume.**



**Ways to create volumes:**

* **Named volumes:**

  * **docker run -v volname:containerpath**
* **Anonymous volumes:**

  * **docker run -v mountpath  ----> basically it is the only container path. if we want temporary storage for the container we can use this.**
* **Bind mount:**

  * **docker run -v hostpath\_dir:contPath\_dir**



**Note!!: From the above three path in the 1st 2 paths the docker manages the volumes where as in the 3rd type the volume is created and managed by operating system of local machine.**



###### **Usage of docker volumes when we are using docker compose:**

* **in docker compose yaml file use direstly delcae volume: -hostdir:container\_dir**

**for example:**



**--------------------------------------------------------------------------------------------------------**



**version:"3.8"**



**services:**

&#x20;   **mongo:**

&#x20;       **image: mongo**

&#x20;       **ports:**

&#x09; **-27017:27017**

&#x20;       **environment:**

&#x20;           **MONGO\_INITDB\_ROOT\_USERNAME:admin**

&#x20;           **MONGO\_INITDB\_ROOT\_PASSWORD:qwerty**

&#x09;**volume:**

&#x09; **- E:/additional\_data\_space:/data/db**

**--------------------------------------------------------------------------------------------------------**



**Docker network:**

1. it is about how the containers are going to interact with the outside world. How incoming and outgoing connections are formed.
2. Drivers in nwteork:

   1. bridge: When ever we create a network by default is made as bridge driver if not specified. the containers created will be attached to the bridge. this is done when one container need to communicate with the another container on the same host machine.
      					
  						container1       container2
 							     |		     |              
 							---------------------------->bridge
   2. host: the container created in the host network uses the same network as the host machine without ip address. 
   3. null: when we need to create a completely isolated container we use this null. It is even isolated with the host machine an isolated with another containers also.





