# Infrastructure Setup

In this document, we will be detailing steps to provision an VM instance on GCP using Compute Engine and setting up the instance required to start the course.

## Pre-requisites

Some pre-requisites includes:
* Create a new Gmail account to gain access to free credits (~$300) for GCP
* Setup GCP account to access free credit and dedicate workspace for this course

# 2. Provisioning GCP VM Instance with GCP

To provision an VM instance, search for `Compute Engine` . You must first enable the API prior to be allowed to setup any instance of your own. As a guideline, I have provision an VM instance of the following configurations:

1. Region: (`asia-southeast1-b (Singapore`)
2. Zone: (`asia-southeast1-b (Singapore`)
3. Series: `E2`
4. Machine type: `Standard-4`
5. Operating system: `Ubuntu 22.04 LTS`
6. Boot disk size: `30GB`

With the free credit provided by GCP, along with your constant diligence in shutting down the VM when not in-used, the setting above will not cost a single cent, while providing a decent amount of computing power required to complete the course.

# 3. Assigning External Public Static IP

As we want to be able to SSH into your VM constantly throughout the duration of the course, shutting down the VM without provisioning a static external IP address would be unwise, as we will need to constantly change the SSH host details, whenever we spin up the instance again.

As such, you will also provision an external public static IP via `VPC Network > IP addresses > Reserve a External Static IP`. If you are interested, my configuration is as such:

1. Network Service Tier: `Premium`
2. IP version: `IPv4`
3. Type: `Regional`

# 4. Enabling SSH 

To enable SSH, we will first need to generate a SSH key using `ssh-keygen` as such:

```bash
ssh-keygen -t rsa -f ~/.ssh/mlops-zoomcamp -C <user>
```

Subsequently, you will need to copy the content of the created key via:

```bash
cat <path>/.ssh/mlops-zoomcamp.pub
```

Where, you will then paste the content into the GCP console under `Settings > Metadata > SSH Keys`.

# 5. SSH Into GCP VM Instance

Once you have added your SSH key to your VM instance, you can officially SSH into the instance via:

```bash
ssh -i ~/.ssh/mlops-zoomcamp <user>@<external static ip of vm>
```

However for sake of convenience, you can add the GCP instance as a host into your SSH config file located at `~/.ssh/config` :

```bash

vim ~/.ssh/config

#### add the lines below into the file ####
Host gcp-mlops-zoomcamp # replace with whatever you want
  HostName <external static ip of vm>
  User <user>
  IdentityFile ~/.ssh/mlops-zoomcamp
  StrictHostKeyChecking no
```

Thereafter, you can SSH into the instance with just:

```bash
ssh gcp-mlops-zoomcamp
```

# 6. Installing Dependencies

## Docker (and Docker-Compose)

### Update Base Image

```bash
sudo apt-get update
```

### Install Docker

```bash
sudo apt-get install docker.io

# run docker to test if installed correctly
docker run hello-world
```

### Install Docker-Compose

```bash
sudo apt-get install docker-compose
```

#### If Facing Permission Issues With Docker

Referring to permission issues with Docker daemon socket: [How to fix docker: Got permission denied while trying to connect to the Docker daemon socket | DigitalOcean](https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket)
##### Create Docker Group

```bash
sudo groupadd docker
```

##### Add Current User To Docker Group

```bash
sudo usermod -aG docker ${USER}

# log out and log in or use the following to evaluate the membership
su -s ${USER}
```

##### Verify Membership Works

```bash
docker run hello-world
```

## Poetry 

Instead of using `anaconda` to manage our environments, I opted to use [poetry](https://python-poetry.org/docs/) to handle all my dependencies throughout the course.