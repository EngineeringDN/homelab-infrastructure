The main "omv" storage is linked through "Datacenter" as
```
ID: omv
Type: NFS
Content: Container
Path/Target: /mnt/pve/omv
Shared: Yes
Enabled: Yes 
```

The mounted storages in `/etc/fstab`are:

```
<OMV_IP>:/cloud /mnt/nas/nc-data nfs4  rw,noatime,_netdev,x-systemd.automount 0 0
<OMV_IP>:/matrixmedia /mnt/stash nfs4 rw,x-systemd.automount 0 0
<OMV_IP>:/export/gitea-repos /mnt/pve/gitea-repos nfs defaults,soft,timeo=50,retrans=5,_netdev 0 0

```


TODO:
- [ ] The whole storage system/structure needs a bit of a rework.
- [ ] More Hard Drives...