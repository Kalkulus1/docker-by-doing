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
