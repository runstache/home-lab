# Airflow Setup

This is a stand-alone Airflow Server

## System Setup

Before installing Airflow we need to configure a few items on the VM.

### Users
The first thing we need to do is setup a system user that the Airflow service will utilize.

```bash
sudo adduser --system --group airflow-user
```

### Airflow Directories

Add the Airflow Home Directory

```bash
# Create the directory
sudo mkdir -p /airflow

# Set the Permissions
sudo chown -R airflow-user:airflow-user /airflow
sudo chmod 777 /airflow

```

### Add Python/Pip

If not already present install python and pip.

```bash
# Update Repos
sudo apt update -y

# Install Python
sudo apt install python3

# Install Pip
sudo apt install python3-pip

# Install Vitrual Env
sudo apt install python3-venv
```

### Kubernetes Configuration

Create a folder in the __Airflow Home__ directory to house the Kubernetes configuration

```bash
sudo mkdir -p /airflow/kubes/
```

Once the directory exists copy the Kubernete config file to this location to provide access to the K3 Cluster.

## Install Postgres

Airflow is backed by a Postgres DB. To install this, run the following:

```bash

# Add Repo for PG 18
sudo apt install -y postgresql-common
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh

# Install PG 18

sudo apt update
sudo apt install postgresql-18

```

### Enable Remote Connections

To to enable remote connections, update the following file: `/etc/postgresql/16/main/postgresql.conf`

```text

listen_addresses = '*'

```

Restrict the CIDR in the `/etc/postgresql/16/main/pg_hba.conf`

```text

host  all  all  10.0.1.0/24  scram-sha-256

```

Open the Ubuntu Firewall

```bash
sudo ufw allow from 10.0.0.0/24 to any port 5432
```

### Database Setup

1. Update the Postgres password: `sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'new_password';"`
2. Connect to the Database
3. Create a new Database for Airflow: `CREATE DATABASE airflow;`

Next we need to setup a user for airflow

```sql
create user airflow_user with password 'password';
GRANT ALL PRIVILEGES ON DATABASE airflow TO airflow_user;
GRANT ALL ON SCHEMA public TO airflow_user;
```

## Environments File

For the Airflow system, we need a few environment variable files. This will be used by the services.

Create a new file in `/etc/default/airflow`

```text
AIRFLOW_HOME="/airflow"
KUBECONFIG="/airflow/kubes/config"
```


## Install Airflow

1. To install airflow, clone the [https://github.com/runstache/airflow-jobs](airflow-jobs) repository.
2. Copy the /config/ folder to the __AIRFLOW_HOME__ directory
3. Set the AIRFLOW_HOME environment variable: `export AIRFLOW_HOME="/airflow"`
3. Create a new virtual environment in the __AIRFLOW_HOME__ locations: `python3 -m venv .venv`
4. Activate the Virtual Environment: `source ./.venv/bin/activate`
5. Execute the Install script in the Config directory

This will install Airflow to the __AIRFLOW_HOME__ location.

## Configure Airflow

The easiest way to get started is to run airflow in standalone mode to generate all of the configuration files.

```bash
# Run Airflow in Standalone

airflow standalone

```

This will create the configuration files. Stop the service and open the Configuration file in `/airflow/airflow.cfg`.

Update the following keys:

```text

dags_folder =/ airflow/jobs

load_examples = False
sql_alchemy_conn = <connection string to postgres>

```

Save the configuration file. This uses the defaults for everything mainly and will get airflow up and running.

## Add the Services

1. Copy the services from the `./airflow/services` directory to `/etc/systemd/system`
2. Enable each service
```bash
# Reload the Daemon

sudo systemctl daemon-realod

# Add the Web Service
sudo systemctl enable airflow.service

# Add the Scheduler
sudo systemctl enable airflow-schedule.service

# Add the Triggerer
sudo systemctl enable airflow-trigger.service

```

## Checking the Services

To inspect any of the services, you can review the service logs

```bash

sudo journalctl -u airflow.service -f

```




