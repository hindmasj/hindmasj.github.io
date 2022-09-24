# WSL Integration For CentOS

[Home](../index) | [Back](wsl-index)

(and Fedora and probably other nixes)

## Docker Settings

You need to Docker that you want it to work with your instance.

1. In Docker Desktop, select settings (the cog wheel), then click "Resources" and "WSL Integration".
1. Find your WSL instance in the list, check it, then click "Save And Restart".
1. I found this did not actually do a restart, so found the docker engine in the task bar (right hand side, up arrow, little whale symbol), then right-click and select "Restart Docker ..."

## Instance Configuration

You need to give your user access to the docker socket. The easiest way is to add your user to the docker group.

````
sudo usermod -aG docker hindmasj
````

You need to start a new shell to pick up the new group.

---
[Home](../index) | [Back](wsl-index)
