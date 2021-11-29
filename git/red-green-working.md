# A Workflow For Jumping Between Red and Greenside working

I often find myself working on projects where the root repository is locked behind a firewall and can only be accessed with a privileged device like a corporate laptop. But the laptop is not powerful enough to do the development on, and I want to use a dedicated machine.

This article shows you a workflow that helps you do this.

## Terminology

To make the description clear I will use the following terminology.

* **Greenside Network**: The firewalled network which needs privilege to connect to.
* **Redside Network**: The Internet.
* **Main Repo**: The protected greenside git repository that myself and my colleagues are all committing to. The main repo cannot be accessed through the firewall.
* **Laptop**: The privileged device that can connect to the greenside network and can interact with the main repo.
* **Workstation**: The unprivileged device that I want to do the bulk of my work on, but cannot connect to the greenside network.
* **USB**: A USB memory stick that can be read by both the laptop and workstation. Would recommend encrypting it.
* **Foo**: The project I am working on.
* **Bar**: The branch I am working on.
* **Develop**: The branch everybody is merging to.
* **Workspace**: The parent directory of the project working directory. I use *${HOME}/workspace*.

# Preparation

## Preparing Greenside

So the main repo already exists and *foo* is an existing project and *develop* is the branch all my colleagues and myself are merging to.

### USB Bare Repo

We will create a bare repository on the USB so that we can use it as remote on both the laptop and the workstation. Plug the USB into the laptop and navigate to the directory where you want the repo to go. I use git-bash for working on the laptop so a sensible place is */d/repos*.

````
cd /d/repos
git clone --bare https://main.repo/foo.git
````

### Laptop Working Directory

On the laptop clone *foo* into my workspace and then add a remote for the bare repo on the USB.

````
cd ~/workspace
git clone https://main.repo/foo.git
cd foo
git remote add usb /d/repos/foo.git
````

So now you have a working directory on the laptop and it has remotes set up. The main repo is called *origin* and the USB stick is called *usb*.

## Preparing Redside

Eject the USB stick from the laptop and plug it into the workstation. We will assume from here on if you are working on a device, the USB will be plugged into that device and correctly mounted.

On the workstation you only need to clone the project from the USB. On my workstation I use WSL instances and [mount the USB on */mnt/d*](../wsl/mount-d-drive). The workflow here is mount the USB, clone the project, then unmount the USB. The working directory on the workstation only has one remote, *origin* which will point back to the USB.

````
dmount
cd ~/workspace
git clone /mnt/d/repos/foo.git
dunmount
````

# TODO

This is a big article so I will work on it in chunks. Still to come:

* Scenarios for creating your working branch
* Scenarios for committing work and keeping all repos up to date.
* Scenarios for merging your work into the develop branch and updating all repos.
* Some general tips for getting out of jail.
