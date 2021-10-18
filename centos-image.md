# Build any distro in WSL2

This uses the trick of exporting a docker container to create the initial distro disk.
See [Microsoft WSL Guide](https://docs.microsoft.com/en-us/windows/wsl/use-custom-distro)

# On An Existing Linux image

You need to use an existing Linux image to pull the image and capture the container.

For Centos 7

````
docker image pull centos:centos7
docker run -t centos:centos7 bash ls /
dockerContainerID=$(docker container ls -a | grep -i centos:centos7 | awk '{print $1}')
docker export $dockerContainerID > /mnt/c/temp/centos7.tar
````

Then repeat for Centos 8

````
docker image pull centos:centos8
...
````

# In Powershell

With the newly created tar of the docker image, you can turn it into a WSL instance.

````
cd C:\temp
mkdir C:\distros\CentOS7
wsl --import CentOS7 C:\distros\CentOS7 .\centos7.tar
````

Inspect the available instances.
````
wsl -l -v
````

Launch and enter your new instance.
````
wsl -d CentOS7
````

# Update And Configure CentOS

Complete the job by updating the instance and setting up a default power user.

````
yum update -y && yum install passwd sudo -y
myUsername=hindmasj
adduser -G wheel $myUsername
echo -e "[user]\ndefault=$myUsername" >> /etc/wsl.conf
passwd $myUsername
````

Exit, make sure the instance is stopped, then restart. You should now be the default user, not root.

````
wsl --terminate CentOS7
wsl -d CentOS7
````
