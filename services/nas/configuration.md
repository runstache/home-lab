# NAS Server Configuration

The NAS Server is configured to provide Spark Jars through Samba Shares. The shares are housed on an attached large volume and shared at specific shares.

## Java POM

The [POM file](./pom.xml) in this folder is used to download additional jars for use by the Spark Cluster. This can be accomplished through Maven with the below command

```bash

# Maven download related JARs
sudo mvn dependency:copy-dependencies -DoutputDirectory=/mnt/nas/my_share

```

## Creating Samba Shares

The below will setup a Samba Share to be used for sharing items to the Spark Cluster

```bash

sudo mkdir /mnt/nas_root/my_share
sudo chmod 777 /mnt/nas_root/my_share 
sudo mkdir -p /mnt/nas_root/my_share

```

Once created, you will setup the share in Samba. Open `/etc/samba/smb.conf` and add a share name like below:

```bash

[nas]
   path = /mnt/nas_root/my_share
   browseable = yes
   read only = no
   guest ok = yes


# Restart the Samba Service
sudo systemctl restart smbd

```

This creates a Samba Share named nas that is accessible without authentication.
