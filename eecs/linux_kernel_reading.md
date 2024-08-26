# Setup Lab Environment
Ubuntu 10.04.4 LTS has kernel 2.6.32. Which is a good candidate for most linux kernel related books.

Download Vmware workstation pro 17. It is free for personal use.

Install VMware workstation pro 17 on a x64 host machine.

Download Ubuntu 10.04.4 from https://old-releases.ubuntu.com/releases/lucid/

Create a VM of Ubuntu 10.04.4

Edit the /etc/apt/sources.list

* change us.releases.ubuntu and security.ubuntu to old-releases.ubuntu
* sudo apt-get update

find the linux kernel images

`sudo apt-cache search linux-image`


