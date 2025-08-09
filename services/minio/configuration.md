# Mino Server Configuration

This is a standalone Mino Server running on a Raspberry Pi4 with a 4TB drive attached.

## Service Account User Setup

Create a User for the Service to use.

```bash

# Create the Group
sudo groupadd -r minio-user

# Add the User
sudo useradd -M -r -g minio-user minio-user

```

## Setup the Extra Drive

After wiping the Drive, add it through the following as a mount

```bash

# Create a Mount point
sudo mkdir /mnt/exdisk

# Get the Device Info
sudo fdisk -l

# Mount the Device
sudo mount /dev/sda2 /mnt/exdisk

# Give Mino User rights to the mount
sudo mkdir /mnt/exdisk/minio
sudo chown -R minio-user:minio-user /mnt/exdisk/minio

```

Add the mount information to `/etc/fstab`

```bash

# Look Update UUID of the Mount

sudo blkid

```

```text
UUID=[Mount UUID]/mnt/exdisk ext4 defaults 0 0
```

## Setup the Services

Create new entries in the `/etc/systemd/system` location for the [minio service](./services/mino.service). Add the entries in the [minio defaults](./services/minio.defaults) file to `/etc/default/minio`.


Register the Services

```bash

# Reload the Daemon
sudo systemctl daemon-reload

# Enable the Service
sudo systemctl enable minio.service

# Start the Service
sudo systemctl start minio.service

```
