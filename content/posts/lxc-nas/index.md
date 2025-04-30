+++
title = "NAS Shares On LXC - Unprivileged"
date = "2025-04-30T12:31:18+00:00"
authors = ["kn"]
cover = "posts/lxc-nas/images.png"
tags = ["lxc", "nas", "proxmox", "sysad", "network"]
keywords = ["lxc", "nas", "proxmox", "sysad", "network"]
description = "how to mount a NAS share in an unprivileged LXC. This is useful for many applications, particularly things like Jellyfin and Plex that often require larger NAS storage connectivty"
showFullConten
+++

# On LXC Create User Group
```
groupadd -g 10000 lxc_shares
```

# Optional: Add Other Users to Group (e.g., Jellyfin, Plex)
```
usermod -aG lxc_shares USERNAME
```

# On Proxmox Host
Create a folder to mount the NAS
```
mkdir -p /mnt/lxc_shares/nas_rwx
```

# Add NAS CIFS share to /etc/fstab
Replace //NAS-IP-ADDRESS with your NAS IP
Replace Username and Passwords
```
{ echo '' ; echo '# Mount CIFS share on demand with rwx permissions for use in LXCs ' ; echo '//NAS-IP-ADDRESS/nas/ /mnt/lxc_shares/nas_rwx cifs _netdev,x-systemd.automount,noatime,uid=100000,gid=110000,dir_mode=0770,file_mode=0770,username=smb_username,password=smb_password 0 0' ; } | tee -a /etc/fstab

```

# Reload daemon

```bash
systemctl daemon-reload
```

# Mount the NAS to the Proxmox Host
```bash
mount /mnt/lxc_shares/nas_rwx
```

# Add Bind Mount of the Share to the LXC Config

Be sure to change the LXC_ID

```bash
You can mount it in the LXC with read+write+execute (rwx) permissions.
{ echo 'mp0: /mnt/lxc_shares/nas_rwx/,mp=/mnt/nas' ; } | tee -a /etc/pve/lxc/LXC_ID.conf

You can also mount it in the LXC with read-only (ro) permissions.
{ echo 'mp0: /mnt/lxc_shares/nas_rwx/,mp=/mnt/nas,ro=1' ; } | tee -a /etc/pve/lxc/LXC_ID.conf
```

then on your lxc you can access it via `/mnt/nas`