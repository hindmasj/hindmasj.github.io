# A Workflow For Jumping Between Red and Greenside working

[Home](../index.md) | [Back](git-index.md)

I often find myself working on projects where the root repository is locked behind a firewall and can only be accessed with a privileged device like a corporate laptop. But the laptop is not powerful enough to do the development on, and I want to use a dedicated machine.

This article shows you a workflow that helps you do this.

* [Terminology](#terminology)
* [Preparation](#preparation)
* [Create The Working Branch](#create)
* [Check Which Branch Is Where](#check)
* [Committing](#committing)

## <a name="terminology"></a>Terminology

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

# <a name="preparation"></a>Preparation

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

# <a name="create"></a>Create The Working Branch

You will be doing the work on a feature branch, which you later merge/drop into develop. You can create this branch either on the main repo, laptop or workstation, and then propagate it to the other locations.

## Create On Main Repo

This is the preferred place to create the branch. You pick up a new ticket and create a feature branch on the main repo using the GUI. Now you need to get this branch onto your workstation.

### On Laptop

On the laptop, you need to checkout the new branch and then push it to the USB. First do a pull to make sure you get updated branch information from the main repo.

```
git pull
git checkout feature/bar
git push usb feature/bar
```

Note that the tracking remote for this branch will be origin.

### On Workstation

Simply checkout the new branch from the USB. Again, pull first to update the list of remote branches.

```
git pull
git checkout feature/bar
```

## Create On Workstation

This is less preferred. You start working on something and realise it needs its own branch. So you create one, then push it all the way back to the main repo. At each step you set the upstream tracking information to make the pulling and pushing consistent.

### On Workstation

Create the new branch with your changes, then push to the USB, setting the upstream tracking information.

```
git branch feature/bar
git checkout feature/bar
git commit -m "some message"
git push -u origin feature/bar
```

### On Laptop

On the laptop you need to update the branch information from the USB, checkout the new branch, and push it the main repo, and reset the tracking so you always have the main repo as the default tracking location.

```
git remote update usb
git checkout feature/bar
git push -u origin feature/bar
```

Setting the upstream is optional, but for consistency it is best to have the tracking upstream point towards the main repo, then you know where you are later when you are pushing and pulling commits.

## Create On Laptop

This is not preferred, but if you get in this situation you need to push both to the main repo and the USB. You would get into this situation if the main repo was not one of the git servers, like Github or Gitlab, so the only way of creating and merging feature branches is to do it in a working directory.

### On Laptop

Create the new branch, push with tracking to the main repo, then push without tracking to the USB.

```
git branch feature/bar
git checkout feature/bar
git commit -m "some message"
git push -u origin feature/bar
git push usb feature/bar
```

### On Workstation

A pull request will update the local refs, then you can just checkout the required branch.

```
git pull
git checkout feature/bar
```

# <a name="check"></a>Check Which Branch Is Where

You can run ``git branch --all -vv`` to get a view of which branches are known about, where they are tracked and if they up to date. Here is the view from the laptop.

```
$ git branch -avv
* feature/from-lap                4a2a68c [origin/feature/from-lap] changed on laptop
  feature/from-ws                 97d0821 [origin/feature/from-ws] Change on ws
  main                            c12dd7c [origin/main] .
  remotes/origin/feature/from-lap 4a2a68c changed on laptop
  remotes/origin/feature/from-ws  97d0821 Change on ws
  remotes/origin/main             c12dd7c .
  remotes/usb/feature/from-lap    4a2a68c changed on laptop
  remotes/usb/feature/from-ws     97d0821 Change on ws
  remotes/usb/main                c12dd7c .
```

From this you can see that the current branch is "feature/from-lap", and it is tracked to origin. The same branch is known about remotely on both "origin" and "usb", and the commit IDs all match so they are synchronised. You can also see the other two branches here are also tracked from origin and synchronised both locally and on usb.

Here is the simpler view from the workstation.

```
git branch --all -vv
* feature/from-ws                 97d0821 [origin/feature/from-ws] Change on ws
  main                            c12dd7c [origin/main] .
  remotes/origin/feature/from-lap c12dd7c .
  remotes/origin/feature/from-ws  97d0821 Change on ws
  remotes/origin/main             c12dd7c .
```

It looks like the workstation does not know about the branch "feature/from-lap". We can fix that with a pull.

```
$ git pull
remote: Counting objects: 5, done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From /home/sjh/git-tests/usb
   c12dd7c..4a2a68c  feature/from-lap -> origin/feature/from-lap
Already up-to-date.

$ git branch --all -vv
* feature/from-ws                 97d0821 [origin/feature/from-ws] Change on ws
  main                            c12dd7c [origin/main] .
  remotes/origin/feature/from-lap 4a2a68c changed on laptop
  remotes/origin/feature/from-ws  97d0821 Change on ws
  remotes/origin/main             c12dd7c .
```

So now the workstation knows about the new branch because it is listed in the remote refs. Checking out that branch will pull all changes locally.

```
$ git checkout feature/from-lap
Branch feature/from-lap set up to track remote branch feature/from-lap from origin.
Switched to a new branch 'feature/from-lap'

$ git branch --all -vv
* feature/from-lap                4a2a68c [origin/feature/from-lap] changed on laptop
  feature/from-ws                 97d0821 [origin/feature/from-ws] Change on ws
  main                            c12dd7c [origin/main] .
  remotes/origin/feature/from-lap 4a2a68c changed on laptop
  remotes/origin/feature/from-ws  97d0821 Change on ws
  remotes/origin/main             c12dd7c .
```

So now the current branch is the laptop one, and by comparing the commit ID it matches what is on the laptop and in the main repo.

# <a name="committing"></a>Committing
Committing is pretty simple. Depending on where you do the commit, you have to take different actions to ensure all of the repos and working directories are up to date.

## Commit On The Workstation
You have made some changes and now you are ready to commit them. Commit then push to the rest of the repos.

### Workstation
Commit the changes and push to the USB.
```
$ git commit -m "a message"
$ git push
```
### Laptop
Plug the USB into the laptop, pull from it, then push to the main repo.
```
$ git pull usb feature/bar
$ git push
```

## Commit On The Laptop
Not preferred, but maybe you spotted a mistake while transferring and fixed it there and then.

### Laptop
Push to both the main repo and the USB.
```
$ git push
$ git push usb feature/bar
```

### Workstation
Update from the USB
```
$ git pull
```

## Commit On The Main Repo
Perhaps someone else has made some changes on your branch and you want them.

### Laptop
Pull from main, push to USB.
```
$ git pull
$ git push usb feature/bar
```

### Workstation
Bring the working directory up to date.
```
$ git pull
```

# TODO

This is a big article so I will work on it in chunks. Still to come:

* Scenarios for merging your work into the develop branch and updating all repos.
* Some general tips for getting out of jail.
