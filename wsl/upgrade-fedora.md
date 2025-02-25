# Upgrade a Fedora WSL Instance

[Home](../index) | [Up](wsl-index)

Thanks to [Jonathon Bowman](https://dev.to/bowmanjd/how-to-upgrade-fedora-in-place-on-windows-subsystem-for-linux-wsl-oh3) for this solution.

Here is how to upgrade in place a Fedora WSL instance using the DNF tools. This method overcomes the problem of not being able to reboot a WSL instance. Note the use of an environment variable to specify no reboot.

## Clean Up and Backup

Optional steps.

From the Fedora command line.

```
sudo dnf clean all
```

Then from a powershell make a backup of the instance to a temporary file.

```
wsl --export fedora $HOME\Downloads\fedora-wsl.tar
```

## Perform the Upgrade

Replace "xx" with whatever the next release number is, e.g. "41".

```
sudo dnf upgrade --refresh
sudo dnf install dnf-plugin-system-upgrade
sudo dnf system-upgrade download --releasever=xx
export DNF_SYSTEM_UPGRADE_NO_REBOOT=1
sudo -E dnf system-upgrade reboot
sudo -E dnf system-upgrade upgrade
sudo dnf upgrade --refresh
```

## Restore From Backup

Only if it all goes wrong! You can also create a separate instance by choosing a new WSL instance name.

```
mkdir C:\distros\freshfedora
wsl --import Fresh-Fedora C:\distros\freshfedora $HOME\Downloads\fedora-wsl.tar
```

---
[Home](../index) | [Up](wsl-index)
