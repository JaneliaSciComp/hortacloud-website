---
title: "Installing Docker"
linkTitle: "Installing Docker"
weight: 40
description: >
  How to install Docker on Oracle Linux 8
---

To install Docker on a server running Oracle Linux 8, some special configuration is needed. Much of this comes from the [official documentation](https://docs.docker.com/install/linux/docker-ce/centos/).

## Prerequisites

First, make sure that Docker isn’t already installed:

```bash
yum list installed | grep docker
```

Remove any existing installations before proceeding.

Ensure that /opt (or whatever disk is to be used for Docker data) is formatted with the d_type option. You can find out like this:

```bash
$ xfs_info /opt
meta-data=/dev/mapper/vg0-lv_opt isize=512    agcount=4, agsize=5701632 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=22806528, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=11136, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

If the above says ftype=0, then the filesystem will need to be recreated ([reference](https://linuxer.pro/2017/03/what-is-d_type-and-why-docker-overlayfs-need-it/)).

## Installing Docker

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce
```

If this fails with error messages like `package containerd.io-1.4.3-3.2.el8.x86_64 conflicts with runc provided by runc` then you may have conflicting packages installed already. Try removing them like this:

```bash
sudo yum erase podman buildah
```

## Post Install Configuration

To avoid running out of space on the root partition, you should configure docker to point to /opt/docker ([reference](https://www.rb-associates.co.uk/blog/move-var-lib-docker-to-another-directory/)):

```bash
sudo mkdir -p /opt/docker
sudo chown root:root /opt/docker
sudo chmod 701 /opt/docker
```

Next, configure Docker to use the overlay2 storage driver ([reference](https://www.projectatomic.io/blog/2015/06/notes-on-fedora-centos-and-docker-storage-drivers/)).

Create a file at /etc/docker/daemon.json with this content:

```json
{
  "data-root": "/opt/docker",
  "storage-driver": "overlay2"
}
```

You should also create a local user called "docker-nobody" with UID 4444, which can be used for running containers without root.

```bash
sudo groupadd -g 4444 docker-nobody
sudo useradd --uid 4444 --gid 4444 --shell /sbin/nologin docker-nobody
```

Finally, you can start Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

## Installing Docker Compose

You'll also need to install the Docker Compose executable:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Note that there are newer versions of the Docker Compose, but they have bugs that prevent them from working with our scripts. Please use the version above to ensure compatibility.
