# Snap.BK

Snap.BK is a backup tool that relies on `btrfs` snapshots therefore it doesn't support any other file systems. You can create snapshots of a `subvolume` anywhere on the disk then Snap.BK has the ability to send the created snapshot to any other disk mounted (or not) on the system or to a remote location.

The tool is focusing on user data over the system. ( OS == cattle, user data == pet)

__IMPORTANT:__ If you configure the send feature (which you should otherwise this is pointless) you need to have the target block device you're sending the snapshot to formatted to `btrfs`.

## How does it work?

_Snap.BK_ does nothing else, but takes snapshot based on a set of predefined values, and enables you to use that snapshot as a proper backup by sending it to an internal, external or remote block device.

## Features

- Create snapshot of btrfs subvolumes,
- Send snapshot to internal, external or remote disks formatted with btrfs,
- Automatically cleans up unneeded snapshots,
- Mounts disks as external stores to send snapshots to,
- Generates `systemd service` and `timer` to automate the process,
- Allows for multiple subvolume snapshot configuration, and generating custom `systemd services` and `timers`.

## Installation

```bash
git clone git@github.com:apinter/Snap.BK.git
cd Snap.bk
sudo install -D -p -m 0755 snapbk /usr/bin/snapbk
```

Once the script is installed you need to create the default configuration folder

```bash
sudo mkdir /etc/snapbk
```

And copy the default config from the git repo:

```bash
sudo cp config.default /etc/snapbk/config
```

Don't forget to modify the config to meet your needs!

Coming soon to _openSUSE_!

## Usage

Before doing anything make sure that you setup the config first in `/etc/snapbk/config`

```bash
    -h, --help              Show this message
    -b, --backup            Start backup procedure
    -s, --setup             Create initial configuration in /etc/snapnbackup/config
    -c, --config            Path to configuration file to be used instead of the default created with --setup 
    -g, --systemd-generate  Generate systemd unit and timer. Can pass OnCalendar value as argument (daily, weekly etc.)
    -k, --systemd-custom    Generate systemd unit and timer. Can pass OnCalendar value and config path as argument (in that order)
    
    Example:

    snapbk -g daily     ## Will generate a daily running timer using the default config path
    snapbk -k daily /etc/snapbk/config.var  ## Will generate a daily running timer using a custom config 

```

## Tip for remote send|receive

The easiest way to back up to remote block storage is by configuring an `ssh` config for the remote for a user that has passwordless `sudo` access. If the remote is publicly accessible the `ssh` connection should be accessible only via some VPN solution. All this is ideal or can conigure `sudoers` for the user to be able to run the `btrfs` commands to without asking for password. Depends on your threat model, and personal preference.

Example `ssh` config:

```bash
Host btrfs-remote
    Hostname 192.168.1.10
    User adathor
    IdentityFile ~/.ssh/btrfs
    Port 22
```

Since the script is running with `root` you probably want to store this in `/root/.ssh/config`.
