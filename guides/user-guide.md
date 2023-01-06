# Table of Contents

- [Table of Contents](#table-of-contents)
- [User Guide](#user-guide)
  - [Accessing the server](#accessing-the-server)
    - [Via SSH](#via-ssh)
    - [Via Remote Desktop](#via-remote-desktop)
  - [Remotely access files on the server](#remotely-access-files-on-the-server)
    - [Using Windows File Manager](#using-windows-file-manager)
    - [Using FileZilla](#using-filezilla)
  - [Advanced Guide](#advanced-guide)
    - [Updating password](#updating-password)
    - [Changing your shell](#changing-your-shell)
    - [Installing software](#installing-software)

# User Guide

## Accessing the server

The access to the server is limited to the HKUST network. You will need to use HKUST VPN or connect your PC to the wired network to be able to access the server. Within the HKUST network, you can access PHZ8213 via SSH or via a remote desktop.

### Via SSH

To access, the server via SSH, simply use your terminal and run:

```sh
ssh username@phz8213.phys.ust.hk
```

### Via Remote Desktop

You can use Windows Remote Desktop or any other RDP client to use the Graphical Interface of the server.

1. Open Remote Desktop Connection
2. Enter `phz8213.phys.ust.hk` as the computer name
3. Enter your username and password

<details>
  <summary>Advanced</summary>

1. Setup SSH key for passwordless login

In your local machine, run:

```sh
ssh-keygen
```

to create a new SSH key pair: `~/.ssh/id_rsa` (private key) and `~/.ssh/id_rsa.pub` (public key). Then open the public key file and copy the content to your clipboard.

Login to the server, and run:

```sh
mkdir ~/.ssh && chmod 700 ~/.ssh #if the directory does not exist
touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
nano ~/.ssh/authorized_keys
```

Paste the content of the public key file into the `authorized_keys` file. Save and exit (ctrl+x). Now you can login to the server without password.

2. Setup SSH config

In your local machine, create a new file `~/.ssh/config` and add the following content:

```sh
Host phz8213
    HostName phz8213.phys.ust.hk
    User username
    IdentityFile ~/.ssh/id_rsa
```

Now you can login to the server by simply running:

```sh
ssh phz8213
```

</details>

<details>
<summary>Troubleshoot</summary>

1. When using Remote Desktop Connection, if you have domain/workgroup set up on your machine, prefix your username with backslash, e.g. `\username` as the username.
2. Make sure you are connected to HKUST network or VPN.
3. Check if the server is online and reachable by running `ping phz8213.phys.ust.hk` in your terminal.

</details>

## Remotely access files on the server

There are two ways to access files on the server:

1. Using Windows File Manager (via Samba)
2. Using FileZilla (via SFTP)

**Read More**: [Guidelines on Managing Data on Server](./guidelines/guidelines-data.md)

### Using Windows File Manager

1. Open Windows File Manager
2. Type `\\phz8213.phys.ust.hk\` in the address bar
3. Login using your username (prefix backslash if your domain is HKUST) and password

### Using FileZilla

1. Open File > Site Manager
2. Create New Site
3. Use Protocol SFTP, Host `phz8213.phys.ust.hk`, Port `22`, Logon Type `Normal`, User `username`, Password `password`
4. Save the site and connect from the dropdown

## Advanced Guide

### Updating password

There are two separate password you need to update, for login and samba.

To change your login password, run:

```sh
passwd
```

To change your samba password, run:

```sh
smbpasswd
```

### Changing your shell

The default shell is `fish`, which is not a unix compliant shell. This implies that some commands you pasted from the internet might not work. There are two alternatives installed on the server: `bash` and `zsh`.

You can temporarily start bash by simply running `bash`, or you can change your shell to `bash` permanently by running:

```sh
chsh -s /usr/bin/bash
```

### Installing software

The recommended way to install software is to use `flatpak` (without root previlage). You can install flatpak packages by running:

```sh
flatpak search <package name>
flatpak install <package name>
```

Alternatively, if you need to run software that is not available in flatpak, you can use docker to run the software in a container. You can find the docker image on [Docker Hub](https://hub.docker.com/).

Read more: [Docker in Rootless mode](https://docs.docker.com/engine/security/rootless/)

```sh
dockerd-rootless-setuptool.sh install #install docker in rootless mode
systemctl --user start docker #start docker daemon in rootless mode
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock
docker context use rootless
```

In docker, you can run any software as if you are running it on your local machine.

You can manage the container using VSCode Remote Container extension. The docker desktop is currently not supported in rootless mode.  <!-- review -->

1. Install [VSCode Remote Development extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
2. Create a new project
3. Run `Dev Container: Add Development Container Configuration Files...` from the command palette, and setup the container
4. Run `Remote-Containers: Reopen in Container` to open the project in the container
5. The VSCode window is now running inside the container, with the project directory mounted to the container