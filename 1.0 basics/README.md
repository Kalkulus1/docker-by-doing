# Basics 
Docker is the leading containerization platform. If you are using containers, you are likely using Docker. In order to work with Docker however, you must have the Docker daemon, and CLI available.

## My requirements
```
One Ubuntu 20.04 server 
Create a non-root user
An account on Docker Hub if you wish to create your own images and push them to Docker Hub
```

## Login in to your instance
```sh
ssh username@<PUBLIC_IP_ADDRESS>
```

## Installing Docker

Install the Docker prerequisites:
```sh
#update your existing list of packages
sudo apt update

#install a few prerequisite packages
sudo apt install apt-transport-https ca-certificates curl software-properties-common

#Then add the GPG key for the official Docker repository to your system
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

#Add the Docker repository to APT sources
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```

## Install Docker
```sh
#Install Docker:
sudo apt install docker-ce -y

#Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it’s running:

sudo systemctl status docker

#Enable it if it's not enabled
sudo systemctl enable --now docker
```

## Executing the Docker Command Without Sudo

```sh
#Configure User Permissions
#Add the lab user to the docker group:

sudo usermod -aG docker ${USER}

# If you need to add a user to the docker group that you’re not logged in as, declare that username explicitly using:

sudo usermod -aG docker username
```

Note: You will need to exit the server for the change to take effect.
You can type:
```sh
su - ${USER}
```

Confirm that your user is now added to the docker group by typing:

```sh
groups
```

## Using the Docker Command

Syntax
```sh
docker [option] [command] [arguments]
```

To view all available subcommands, type:

```sh
docker
```

## Working with Docker Images

Run a Test Image
Using docker, run the hello-world image to verify that the environment is set up properly:

```sh
docker run hello-world
```

## Working With Prebuilt Docker Images

### Explore Docker Hub
```
Sign in to Docker Hub.
At the top of the page, search for "httpd".
In the left-hand menu, filter for Application Infrastructure, and Official Images.
Select the httpd project.
At the top of the page, click the Tags tab.
Under latest, select linux/amd64.
Back in the list of available images, select nginx
Review the How to use this image section.
```

### Working with httpd
In the Docker Instance, verify that docker is installed:

```sh
docker ps
```

Using docker, pull the httpd:2.4 image:
```sh
docker pull httpd:2.4
```

Run the image
```sh
docker run --name httpd -p 8080:80 -d httpd:2.4
```
This runs on port 80 on the docker container and exposed on port 8080 on the server. 

Check the status of the container:
```sh
docker ps
```

In a web browser, test connectivity to the container:
```sh
<PUBLIC_IP_ADDRESS>:8080
```

#### Run a Copy of the Website in httpd

Clone the demo repository:
```sh
git clone https://github.com/ktechhub/web-test.git

cd web-test
```

Stop the httpd container:
```sh
docker stop httpd
```

Remove the httpd container:
```sh
docker rm httpd
```

Verify that the container has been removed:
```sh
docker ps -a
```


Run the container with the website data:
```sh
docker run --name httpd -p 8080:80 -v $(pwd):/usr/local/apache2/htdocs:ro -d httpd:2.4
```

Verify that the container is running:
```sh
docker ps -a
```

In a web browser, test connectivity to the container:
```sh
<PUBLIC_IP_ADDRESS>:8080
```

### Working with Nginx

Using docker, pull the latest version of nginx:
```sh
docker pull nginx
```

Verify that the image was pulled successfully:
```sh
docker images
```
Run the container using the nginx image:
```sh
docker run --name nginx -p 8081:80 -d nginx 
```

Check the status of the container:
```sh
docker ps
```

Verify connectivity to the nginx container:
```sh
<PUBLIC_IP_ADDRESS>:8081
```

#### Run a Copy of the Website in Nginx

Stop and remove the nginx container:
```sh
docker stop nginx
docker rm nginx
```

Verify that the container has been removed:
```sh
docker ps -a
```

Run the nginx container, and mount the website data:
```sh
docker run --name nginx -v $(pwd):/usr/share/nginx/html:ro -p 8081:80 -d nginx
```

Check the status of the container:
```sh
docker ps
```

In a web browser, verify connectivity to the container:
```sh
<PUBLIC_IP_ADDRESS>:8081
```

Stop the nginx container:
```sh
docker stop nginx
```

Remove the nginx container:
```sh
docker rm nginx
```

Verify that the container has been removed:
```sh
docker ps -a
```


## Handcrafting a Container Image

### Get and Run the Base Image

Retrieve the httpd image:
```sh
docker pull httpd:2.4
```
Run the image:
```sh
docker run --name webtemplate -d httpd:2.4
```
Check the status of the webtemplate container:
```sh
docker ps
```

### Install Tools and Code in the Container

Log in to the container:
```sh
docker exec -it webtemplate bash
```

Run apt update and install git
```sh
apt update && apt install git -y
```

Clone the website code from GitHub:
```sh
git clone https://github.com/ktechhub/web-test.git /tmp/web-test
```
Verify that the code was cloned successfully:
```sh
ls -l /tmp/web-test
```

List the files in the htdocs/ directory:
```sh
ls -l htdocs/
```

Remove the index.html file:
```sh
rm htdocs/index.html
```
Copy the webcode from /tmp/ to the htdocs/ folder:
```sh
cp -r /tmp/web-test/* htdocs/
```
Verify that they were copied over successfully:
```sh
ls -l htdocs/
```
Exit the container:
```sh
exit
```

### Create an Image from the Container
Copy the Container ID:
```sh
docker ps
```

Create an image from the container:
```sh
docker commit <CONTAINER_ID> example/webtest:v1
```

Verify that the image was created successfully:
```sh
docker images
```

Take note of the image size.

### Clean up the Template for a Second Version
Log in to the container:
```sh
docker exec -it webtemplate bash
```
Remove the cloned code from the /tmp/ directory:
```sh
rm -rf /tmp/web-test/
```

Use apt to uninstall git and clean the cache:

```sh
apt remove git -y && apt autoremove -y && apt clean 
```

Exit the container:
```sh
exit
```

Check the status of the container:
```sh
docker ps
```

Create an image from the updated container:

```sh
docker commit <CONTAINER_ID> example/webtest:v2
```

Verify that both images are now running:
```sh
docker images  
```

Delete the v1 image:
```sh
docker rmi example/webtest:v1
```


### Run Multiple Containers from the Image
Run multiple containers using the new image:
```sh
docker run -d --name web1 -p 8081:80 example/webtest:v2
docker run -d --name web2 -p 8082:80 example/webtest:v2
docker run -d --name web3 -p 8083:80 example/webtest:v2
```

Check the status of the containers:
```sh
docker ps
```

Stop the base webtemplate image:
```sh
docker stop webtemplate
```

Verify that only the created containers are running:
```sh
docker ps
```

Using a web browser, verify that the containers are running successfully:
```sh
<SERVER_PUBLIC_IP_ADDRESS>:8081
<SERVER_PUBLIC_IP_ADDRESS>:8082
<SERVER_PUBLIC_IP_ADDRESS>:8083
```