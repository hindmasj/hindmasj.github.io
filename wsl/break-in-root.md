# Break Into An Instance As Root

[Home](../index) | [Up](wsl-index)

From time to time you might come across a terminal instance that when launched has a fault with running sudo. So as you do not have the root password, how do you get in as root?

As this is being run by WSL, not docker, you need to use WSL commands.

1. Close any running terminals.
1. Open a windows command line terminal.
1. Check the name of your instance by running ``wsl --list``.
1. Run a shell as root using ``wsl --user root --distribution mydistro``.
1. Make your changes and exit.

For example, make sure sudo is installed and your default user has full access.

```
> wsl --user root --distribution RockyLinux-9-Base

# dnf install sudo
# usermod -G wheel myuser
# exit
```

---
[Home](../index) | [Up](wsl-index)
