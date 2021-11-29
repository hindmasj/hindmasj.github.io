# Mount and Unmount the D Drive

A common action is to want to mount or unmount a USB stick into the WSL instance. This is usually mounted as *D:* on Windows and I want to mount it as */mnt/d*.

## Mounting Script

Create a script called *mount-d-drive.sh*.

````
#!/usr/bin/env bash
# Mount the current D: drive on /mnt/d as the current user

user=$(id -ur)
group=$(id -gr)
mp="/mnt/d"

sudo mkdir -p ${mp}
sudo mount -t drvfs D:\\ ${mp} -o uid=${user},gid=${group}
````

## Unmounting Script

Likewise the unmounting script.

````
#!/usr/bin/env bash
# Unmount whatever is on /mnt/d

mp="/mnt/d"
sudo umount ${mp}
````

## Create Aliases

Set the executable bit on both scripts. Get both scripts onto your PATH. What I do is have a git repo that includes these types of files in a bin sub-directory. Then I soft link the directory *${HOME}/.local/bin* to that sub-directory.

Then add the aliases to *${HOME}/.bashrc*. I like to have a file *local_bashrc* in the same git repo as the scripts, so I can keep the definitions in there.

In *local_bashrc*

````
# Local aliases
alias dmount='mount-d-drive.sh'
alias dunmount='unmount-d-drive.sh'
````

And in *~/.bashrc*

````
# Source version controlled definitions
. ${HOME}/workspace/miscellaneous/local_bashrc
````

Create a new shell and you will have the aliases ``dmount`` and ``dunmount`` available to you. Note that both use *sudo* so be prepared to enter your password when you use the scripts.
