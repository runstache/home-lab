# General VM Configuration Items

## Disk Volume Resizing

The disks mount in the Proxmox VM without all of the drive space made available. Below are the commands to resize the volumes to consume all of the available space.

### Disk Information

```bash

sudo fdisk -l
sudo df -h
sudo lsblk
sudo pvs
sudo vgdisplay
sudo lvdisplay

```

### Resizing Volumes

Using the information, the below commands will resize the volumes. Update the volume information based on the outputs from the Disk Information section.

```bash

sudo pvresize /dev/mapper/ubuntu--vg-ubuntu--lv /dev/sda3
sudo lvresize /dev/ubuntu-vg/ubuntu-lv /dev/sda3
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv

```

## Mounting additional Volumes

The below can be used on a VM to mount additional volumes

```bash

# Make the mount point directory
sudo mkdir /mnt/extdrive

# Find the Device (/dev/sdb)
sudo fdisk -l

# Make a Filesystem (Optional)
sudo mkfs -t ext4 /dev/sdb

# Mount the volume
sudo mount /dev/sdb /mnt/extdrive

```

Once Mounted Add the following to `/etc/fstab`. This will remount the location on reboot.

```bash
# Look Update UUID of the Mount

sudo blkid

```

```text

UUID=[Mount Location UUID] /mnt/my_share ext4 defaults 0 2

```
