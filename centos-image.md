# Build any distro in WSL2

See [Microsoft WSL Guide](https://docs.microsoft.com/en-us/windows/wsl/use-custom-distro) 
# On existing Linux image

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

# In powershell

````
cd C:\temp
mkdir C:\distros\CentOS7
wsl --import CentOS7 C:\distros\CentOS7 .\centos7.tar

wsl -l -v

wsl -d CentOS7
````

# Update and configure CentOS

````
yum update -y && yum install passwd sudo -y
myUsername=sjh
adduser -G wheel $myUsername
echo -e "[user]\ndefault=$myUsername" >> /etc/wsl.conf
passwd $myUsername
````

Exit, make sure the instance is stopped, then restart. You should now be the default user, not root.

````
wsl --terminate CentOS7
wsl -d CentOS7
````

