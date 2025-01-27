Follow these simple steps to install a new instance of Supervisely to your server.

## Pre-requirements

Before you start installation of Supervisely to your host, please make sure your server meets the hardware & software requirements below.

### Hardware

The minimum hardware requirements are as following:

 - CPU: 4 vCPUs / cores
 - Memory: 8 GB RAM
 - Hard drive: 100 GB storage or more

Choose the hard drive size according to your needs. There is also an option to attach any S3 compatible storage to distribute the data in your cloud.

{% hint style="info" %}
If you don't have enough free space on a system drive, but you have mounted another one, you can change the place where the data is stored by changing `DATA_PATH` in `.env` file
{% endhint %}

{% hint style="danger" %}
Never set `DATA_PATH` pointing to a network share (NFS or SMB), because it affects performance significantly. Set `DATA_PATH` pointing to an actual directory on your local hard drive and then mount `storage` subfolder of `DATA_PATH` directory as a network share - this way "hot" subfolders like `db` or `proxy_cache` will be on a local hard drive and image / videos storage on a network share.
{% endhint %}

#### GPU instance deployment

If you plan to use smart tool (AI powered semantic segmentation) or neural networks (human-in-the-loop) you will need a server with GPU. The requirements are as following:

 - GPU Memory: 8 GB or more (GeForce GTX 1080, Tesla K80, Tesla P100)

You can install Supervisely and run GPU computations on different machines using agents (see [cluster](../../customization/agents/README.md)). 

#### AWS & cloud providers

We recommend to use the following EC2-instances for deployment in AWS:

- 1 × m5.2xlarge — the platform itself
- 1 × p2.xlarge — (for Neural Network Module only)

### Software

To run Supervisely you will need Linux OS with kernel 3.10 or newer. The following configuration is recommended:

- OS: Ubuntu 18.04 or later
- NVIDIA graphics driver: 460.27.03 or later (for Neural Network module only)
- nvidia-docker2 (for Neural Network module only)

All pre-requirements can be automatically installed via `supervisely-cli` utility for Ubuntu 18.04 or later. In case of a different linux distribution, you can install Supervisely and pre-requirements manually, but we won't be able to provide installation support.

## Installation

When all requirements above have been installed, you can deploy Supervisely. Please follow those steps one by one. Read each step carefully and complete it fully before going to the next step.

### Step 1. Get your unique key from us

Before the installation we will send you a license key and `supervise-cli` installation command. Please run it on the machine where you want to install Supervisely.

### Step 2. Install pre-requirements

Run `sudo supervisely install-all` in your terminal. We will detect the necessary dependencies and install them. The following software may be installed:

- [Docker CE](https://docs.docker.com/engine/installation/)
- [Docker Compose](https://github.com/docker/compose/releases)
- [CUDA 9.0 & Nvidia Driver](https://github.com/NVIDIA/nvidia-docker/wiki/Frequently-Asked-Questions#how-do-i-install-the-nvidia-driver) (for Training Module)
- [NVIDIA Container Runtime](https://github.com/NVIDIA/nvidia-docker#quickstart) (for Training Module)

If don't have NVIDIA drivers and CUDA, you will be asked if you need to install it. Choose `y` if you have GPU on your server. Your computer will be rebooted during the installation of the NVIDIA driver.

### Step 3. Run installation command

Run `sudo supervisely init "<your license key>"`. If this is your first time running this command, you will see three prompts, asking you to choose where to store the CLI configuration file (first prompt), Supervisely configuration files (second prompt) and backups (the last prompt).

```bash
[0] /opt/supervisely
[1] /supervisely
[2] /root/.supervisely
[3] /root/.supervisely
[4] /root/.config/supervisely

Couldn't find Supervisely config. Please choose the new location:
```

You can select one of the suggested options or enter your custom location. If you are not sure which option to choose, you can select the default (`/opt/supervisely`).

You can always check where is your configuration and backups locations using `supervisely where` command.

### Step 4. Wait for the installation to complete

Now, the CLI will automatically do the following steps, required to set your Supervisely instance:

1. Download docker-compose configuration files
2. Login to our docker registry
3. Pull and run docker containers
4. Initilize the database and download some sample data
5. Install the license key
6. Deploy the default Supervisely Agent

### Step 5. Start using Supervisely!

Now you can refresh the current page. You will see the login box:

![](screenshot-dev-supervise-ly-login-1658511306465.jpg)

Enter the default credentials: login and password `admin`. Now your are in. Welcome!

{% hint style="info" %}
We strongly advice you to complete [post-installation steps](../post-installation) after the initial setup.
{% endhint %}

## Troubleshooting

#### I open "http://localhost" but see an error

Database is still initializing. Wait a minute and try again.

#### I try to login but see "You have no permissions for this action. Please, contact administrator."

One of our internal services haven't got the license key. Refresh the page and try again.

#### My "admin / Main Node" agent is stuck in "Waiting" status

It seems the agent has tried to wait for Supervisely to start. Restart agent manually: `sudo supervisely deploy-agent`. 

#### When i deploy an agent i see a "runtime" error

Nvidia container runtime has not been installed. Try to run command `docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi` to see if it works.

If you don't want to run train & inference tasks, please remove checkbox "Use nvidia runtime" under advances settings in the new node modal window and run the deploy command again.
