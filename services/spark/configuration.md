# Spark Cluster Configuration

This is the configuration steps to setup a new Spark Standalone Cluster. Yarn or K8 are not used.

## Shared Jars Space

After Spark is installed, you need to create a mount to the Samba location that contains the Shared JAR files for all Spark workers.

```bash

# Create the Mount
sudo mount -t cifs //samba server/nas /mnt/jars -o guest

```

Once the Mount is established, add the following to `/etc/fstab`

```text
//samba-server/nas /mnt/jars cifs guest,uid=1000,gid=1000 0 0
```

## Services

The current cluster contains two VM Servers. One for the Driver and one for the Worker. The following services are established on the machines

### Driver Services

* Spark Master
* Spark Worker
* Spark Connect

### Worker Services

* Spark Worker


## Service Accounts

We use a service account to run the Spark Services. Below are the commands to setup that service account

```bash
sudo groupadd --system spark-user
sudo useradd --system --gid spark-user spark-user
sudo mkhomedir_helper spark-user

# Give it rights to spark directories
sudo chown -R spark-user:spark-user /spark

```

## Spark Services

Use the Service files in this directory to create the services for the Spark items. Create each file in `/lib/systemd/system`. Then enable them through `systemd`

```bash

# Enable Services in System D
sudo systemctl daemon-reload

sudo systemctl enable spark.service
sudo systemctl enable spark-worker.service
sudo systemctl enable spark-connect.service

# Start the Services

sudo systemctl start spark.service
sudo systemctl start spark-worker.service
sudo systemctl start spark-connect.service

# Check Status
sudo journalctl -f -u spark.service

```