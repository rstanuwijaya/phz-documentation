# Table of Contents

- [Table of Contents](#table-of-contents)
- [Administrator Guide](#administrator-guide)
  - [Setting up a new user](#setting-up-a-new-user)
  - [Setting up a new user with sudo access](#setting-up-a-new-user-with-sudo-access)
  - [Setting up a new group](#setting-up-a-new-group)
  - [Set user as inactive](#set-user-as-inactive)
  - [Advanced samba configuration](#advanced-samba-configuration)
  - [Firewall configuration](#firewall-configuration)
  - [Reset all user password](#reset-all-user-password)
  - [System recovery](#system-recovery)


# Administrator Guide

The following guide is intended for administrator use only. Assume all commands to be run as root.

## Setting up a new user

Create a new user

```sh
adduser username --shell /usr/bin/fish # add user and set shell to fish
smbpasswd -a username # add user to samba
passwd username # set password
/root/configure-samba.sh # configure samba
```

To add user to a group, run

```sh
usermod -aG groupname username
/root/configure-samba.sh # configure samba
```

## Setting up a new user with sudo access

```sh
usermod -aG sudo username
```

## Setting up a new group

```
groupadd -g <gid> groupname
```

**Important:** set `gid` to a number to a number $500 < x < 600$ for convenience.

## Set user as inactive

When a user finished their program of study, you can set their account as inactive by running:

```sh
usermod -aG inactive username
/root/configure-samba.sh
```

Please review the code on configure-samba.sh carefully before running it. This will have the following implications:
1. The user will lose their write permission to their user samba share (their samba folder now is owned by root)
2. The user will be removed from all projects groups, e.g. *quantum* and lose their write permission to all projects sambashare

## Advanced samba configuration

Edit `/etc/samba/smb.conf` (also hardlinked to `/root/smb.conf`). Read [smb.conf documentation](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html) for more details.

If a user needs to reset their password, you can run `smbpasswd username` as root.

You can also modify the samba configuration script:

```sh
#! /bin/sh
# path: /root/configure-samba.sh

set -u

IGNORE_LIST="samba vncuser root active projects"
PROJECTS_LIST="quantum group2"
PAST_MEMBERS_LIST=$(sudo groupmems -g inactive -l)

ALL_MEMBERS_DIR=/home/samba/profiles/all-members
CURRENT_MEMBERS_DIR=/home/samba/profiles/current-members
PAST_MEMBERS_DIR=/home/samba/profiles/past-members
PROJECTS_DIR=/home/samba/profiles/projects

for u in $(ls /home); do
	echo $IGNORE_LIST | grep -w -q $u && echo "Skipping $u" && continue
	echo "Checking $u"
	[ ! -d $ALL_MEMBERS_DIR/$u ] && sudo mkdir $ALL_MEMBERS_DIR/$u
	sudo chown $(stat -c "%U:%G" /home/$u) $ALL_MEMBERS_DIR/$u
	sudo [ ! -d /home/$u/Samba/ ] && sudo ln -sf $ALL_MEMBERS_DIR/$u /home/$u/Samba
	# sudo rm /home/samba/profiles/$u/$u
done

sudo -u samba -- bash -c "set -u; rm $CURRENT_MEMBERS_DIR/* $PAST_MEMBERS_DIR/* $PROJECTS_DIR/*"

for u in $(ls /home); do
	if echo $IGNORE_LIST | grep -w -q $u; then
		echo "Ignoring $u"
		continue
	elif echo $PROJECTS_LIST | grep -w -q $u; then
		echo "Adding symlink $u to projects folder"
		sudo chmod ug+ws $ALL_MEMBERS_DIR/$u
		sudo ln -sf $ALL_MEMBERS_DIR/$u $PROJECTS_DIR
	elif echo $PAST_MEMBERS_LIST | grep -w -q $u; then
		echo "Adding symlink $u to past members folder"
		sudo ln -sf $ALL_MEMBERS_DIR/$u $PAST_MEMBERS_DIR

		# remove from all projects group 
		for g in $PROJECTS_LIST; do
			(groups $u | grep -w -q $g) && sudo gpasswd -d $u $g
		done
		
		# make the directory read only by removing permission
		sudo chown root:root $ALL_MEMBERS_DIR/$u
	else 
		echo "Adding symlink $u to active members folder"
		sudo ln -sf $ALL_MEMBERS_DIR/$u $CURRENT_MEMBERS_DIR
	fi
done
```

## Firewall configuration

Check firewall status:

```sh
ufw status numbered
```

Allow port 22 (ssh):
```sh
ufw allow 22
```

Remove rule numbered 2:
```sh
ufw delete 2
```

## Reset all user password

Please double check with caution when using the following script. It will reset all user password to a random password

```sh 
#!/bin/sh
# path: /root/passwd-config.sh

# This script will reset all user password and set it to expire immediately

set -u

USER_LIST=$(echo "user1 user2 user3"| xargs)

echo "generating password for $USER_LIST" > userpassword
echo "$(date)" | tee -a userpassword

for user in $USER_LIST; do
	#pass=$(cat /proc/sys/kernel/random/uuid | sed 's/[-]//g' | head -c 20)
	pass=$(openssl rand -base64 12)
	echo $user:$pass | tee -a userpassword	
	echo $user:$pass | chpasswd
	passwd -e $user
	chsh -s /usr/bin/fish $user
	(echo $pass; echo $pass) | smbpasswd -a -s $user
done

```

## System recovery

In case of system failure, you can recover the system by following the steps below:

1. Prepare a USB drive with Ubuntu 22.04 LTS
2. Find Rocky/IT administrator to authorize you to the ITSC server room.
3. Boot from the USB drive, install timeshift from apt
4. Recover the snapshot of the root partition

