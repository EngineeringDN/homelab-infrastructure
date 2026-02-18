# LXCs and VMs:
## 101 / Portainer (Debian)
This has Portainer running on it. Although it is advised to use a VM for Docker in Proxmox, I never had any problems with it and have not heard about problems. Therefore i am using LXCs for this instance.

### Resources
#### Memory and Cores
Memory: 8 GiB (8192 MiB)
Swap: 4 GiB (4096 MiB)
Cores: 4

#### Storage from OMV
Root Disk:
`local-lvm,size=64GB`
I have a Mount Point configured for this LXC:
```
/mnt/pve/omv --> /data
```

#### Devices
```
Device (dev0): /dev/dri/renderD128
Device (dev1): /dev/dri/card0
```
This is so if I need to encode videos it will use the `Intel Quick Sync` Function from the CPU

## 102 / Nextcloud (Ubuntu)
This has Nextcloud running on it.

### Resources
#### Memory and Cores
Memory: 4 GiB (4096 MiB)
Swap: 2 GiB (2048 MiB)
Cores: 4

#### Storage from OMV
Root Disk:
`local-lvm,size=32GB`
I have a Mount Point configured for this LXC:
```
/mnt/nas/nc-data --> /srv/nextcloud-data,backup=1
```

## 106 / Glance (Debian)
This has Glance running on it.
Which is basically a Dashboard/Startpage.
### Resources
#### Memory and Cores
Memory: 1 GiB (1024 MiB)
Swap: 512 MiB
Cores: 1

#### Storage from OMV
Root Disk:
`local-lvm,size=12GB`

## 107 / Gitea (Debian)
This has Gitea running on it, but it is currently not used yet.

### Resources
#### Memory and Cores
Memory: 2 GiB (2048 MiB)
Swap: 512 MiB
Cores: 2

#### Storage from OMV
Root Disk:
`local-lvm,size=15GB`
I have a Mount Point configured for this LXC:
```
/mnt/pve/gitea-repos --> /var/lib/gitea/data/repositories
```

## 109 / localwebapps (Debian)
This has my personal Webapps running on it like my very simple WIP [Application Tracker](https://github.com/EngineeringDN/ApplicationTracker).

### Resources
#### Memory and Cores
Memory: 2 GiB (2048 MiB)
Swap: 512 MiB
Cores: 1

#### Storage from OMV
Root Disk:
`local-lvm,size=12GB`