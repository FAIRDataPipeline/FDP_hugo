---
weight: 1
title: "Installation"
---

# Installation
The remote registry is a [Django](https://www.djangoproject.com/) web application and REST API and can easily be installed on any server which meets the following requirements.

## Pre-requisites
To install a remote registry you will require the following:

A server capable of running a webserver with the following installed:

### [git](https://git-scm.com/downloads)
Available through package managers such as `apt`, `brew` or `chocolatey`

### [Python 3.10+](https://www.python.org/downloads/)
With `pip` and the `venv` module installed.
Available through package managers such as `apt`, `brew` or `chocolatey`

### A database
Such as `PostgreSQL`(recommended), `MariaDB`, `MySQL`, `Oracle`, `SQLite` (not recommended for production). Full database support can be found on the [django site](https://docs.djangoproject.com/en/5.2/ref/databases/)

N.B. You may also need to install the corresponding database connector (the PostgreSQL connector, `psycopg`, is included in the registry requirements)

### [`Graphviz`](https://graphviz.org/)
`Graphviz` is needed to create provenance reports, full installation instructions can be found on the [`Graphviz` site](https://graphviz.org/download/)

### An S3 Object Storage
This can be any S3 compatible storage e.g. [AWS S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html), [Openstack](https://www.openstack.org/) or [SeaweedFS](https://github.com/seaweedfs/seaweedfs). The registry signs its download and upload URLs with S3 Signature Version 4, so the storage must accept SigV4 presigned URLs.

If you are self hosted we would recommend using [SeaweedFS](https://github.com/seaweedfs/seaweedfs), which provides an [S3 compatible API](https://github.com/seaweedfs/seaweedfs/wiki/Amazon-S3-API). A single server with the S3 gateway enabled can be started with:

```
weed server -dir=<data directory> -s3
```

The S3 gateway listens on port `8333` by default. Set up an access key and secret key as described in [S3 Credentials](https://github.com/seaweedfs/seaweedfs/wiki/S3-Credentials), then create a bucket for the registry with any S3 client, e.g. the [AWS CLI](https://docs.aws.amazon.com/cli/):

```
aws s3 mb s3://<bucket name> --endpoint-url http://localhost:8333
```

## Installation on a Debian based system e.g. Ubuntu
This installation guide will focus on debian based systems but can be easily adapted.

### Step 1. Clone the repository
Clone the dataregistry repository using `git`

```
git clone https://github.com/FAIRDataPipeline/data-registry.git
```

### Step 2. Create and activate a Python virtual environment (`venv`) in the repository folder

```
python3 -m venv venv
source venv/bin/activate
```

### Step 3. Install the `python` requirements

```
pip3 install -r requirements.txt
```
***N.B.*** the same requirements are used by the local and remote registry

### Step 4. Generate a secret key

```
python3 -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```

### Step 5. Edit drams/settings.py
The `settings.py` file within the `drams` folder should be modified to match your requirements.

#### Add your secret key
The secret key you generated in the previous step can either be added directly to the `settings.py` file:

```
SECRET_KEY = '<generated secret key>'
```
**N.B** If you do this remember to remove the following lines:
```
with open('/home/ubuntu/secret_key.txt') as f:
    SECRET_KEY = f.read().strip()
```

Alternatively the secret key can be stored in a file and read. To do this edit the following line with the file path a text file containing your secret key:
```
with open('/home/ubuntu/secret_key.txt') as f:
    SECRET_KEY = f.read().strip()
```

#### Edit `ALLOWED_HOSTS`

Add your domain to `ALLOWED_HOSTS` replacing `data.fairdatapipeline.org`

### Edit `DOMAIN_URL`

Replace the `DOMAIN_URL` with your fully qualified domain url followed by a trailing slash e.g. `https://data.fairdatapipeline.org/`

#### Edit `DATABASES`
The `databases` dictionary (fields) should be modified to contain a single `default` database containing the following fields:
`ENGINE` the engine for your database see the [django documentation](https://docs.djangoproject.com/en/5.2/ref/databases/) for more details.
`NAME` the database names
`USER` username of a user with read and write access to the database
`PASSWORD` password of the above user
`HOST` domain or ip of the database server
`PORT` port the database is running on

#### Edit `BUCKETS`
The `BUCKETS` dictionary should be modified to contain a single `default` S3 bucket containing the following fields:

`url` the fully qualified URL of the S3 server e.g. `https://s3.domain.com:port/`, the port is optional if not running on the default port. For SeaweedFS this is the address of the S3 gateway, e.g. `https://s3.domain.com:8333/`. For AWS the endpoint URL for different regions can be found in the [AWS documentation](https://docs.aws.amazon.com/general/latest/gr/rande.html)

`bucket_name` the name of the bucket

`access_key` access key to bucket belonging to a user with read and write access

`secret_key` matching secret key to the above

`duration` how long URLs generated for the bucket should be valid for in seconds (the template uses 86400, one day). An upload URL must outlive the slowest upload of the largest file, as the store refuses an upload once its URL has expired


#### Set Up Authentication
The remote registry is designed to be authenticated with GitHub or GitLab OAuth, to do this you need to create either a [GitHub OAuth App](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/creating-an-oauth-app) or a [GitLab Oauth application](https://docs.gitlab.com/ee/integration/oauth_provider.html).

To configure these you will need the following callback URLs
**GitHub:**
```
<registry_url>/complete/github/
```
**GitLab:**
```
<registry_url>/complete/gitlab/
```
Where `<registry_url>` is the fully qualified url of the registry e.g. `https://data.fairdatapipeline.org`

Once the application has been setup the following fields will need to be added to settings.py:

**GitHub:**
```
SOCIAL_AUTH_GITHUB_KEY = '<key>'
SOCIAL_AUTH_GITHUB_SECRET = '<secret>'
AUTH_METHOD = "GitHub"
```
Replacing the key and secret from the GitHub OAuth App.

**GitLab:**
```
SOCIAL_AUTH_GITLAB_SCOPE = ['api']
SOCIAL_AUTH_GITLAB_KEY = '<key>'
SOCIAL_AUTH_GITLAB_SECRET = '<secret>'
AUTH_METHOD = "GitLab"
```
Replacing the key and secret from the GitLab OAuth Application

**N.B. if you are running your own instance of GitLab, you will also need to add the following field:
```
SOCIAL_AUTH_GITLAB_API_URL = 'https://example.com'
```
Where `https://example.com` is the full url to your GitLab domain adding the port if not running on the default port e.g. `https://example.com:1234`

### Authenticating Users
With the above authentication, by default anyone with an account on the given provider can login to the data registry.

**To restrict access:**
The registry can accept a yaml file containing users which are allowed to log into the registry. To do this create a yaml file containing the following:
```
username: GitHub Username [required string]
email: email address [optional string]
fullname: Full Name [required string]
orgs: organisation names [optional array]
```

Then add the following field to the `settings.py` file with full path to the yaml file:
```
AUTHORISED_USER_FILE = 'authorised_users/authorised_users.yaml'
```
an example of this yaml file is located on the [FAIRDataPipeline/authorised_users repository](https://github.com/FAIRDataPipeline/authorised_users/blob/main/authorised_users.yaml)

### Step 6: Initialise the registry
The registry should now be initialised, to do this run the following commands from the folder where you cloned the registry:
```
source venv/bin/activate
export DJANGO_SETTINGS_MODULE="drams.settings"
export DJANGO_SUPERUSER_USERNAME=admin
export DJANGO_SUPERUSER_PASSWORD=password
export FAIR_USE_SUPERUSER="True"
cd scripts
chmod +x rebuild.sh
./rebuild.sh
```
Replacing `admin` and `password` with your desired superuser account.

### Step 7: Configure the Service using gunicorn
Create the following files: 

**`/etc/systemd/system/gunicorn.socket`**
```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/home/ryan/data-registry/gunicorn.sock

[Install]
WantedBy=sockets.target
```
Replacing `/home/ryan/data-registry/` with the directory where you cloned the git repository

**`/etc/systemd/system/gunicorn.service`**
```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=ryan
Group=ryan
WorkingDirectory=/home/ryan/data-registry
Environment="DJANGO_SETTINGS_MODULE=drams.settings"
ExecStart=/home/ryan/data-registry/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/home/ryan/data-registry/gunicorn.sock \
          drams.wsgi:application

[Install]
WantedBy=multi-user.target
```
Again replacing both instances of `/home/ryan/data-registry/` with the directory where you cloned the git repository

#### Enable and Start the Service
Run the following commands to enable and run the data registry service
```
sudo systemctl start gunicorn.socket
sudo systemctl enable gunicorn.socket
```

### Step 8: Configure a reverse proxy with nginx
Edit or add a site to your nginx sites-enabled (for this example we will use the default site at `/etc/nginx/sites-enabled/default`). If you choose to add a site, remember to symlink the file to the sites-available directory.
```
server {

    listen 80 default_server;
    listen [::]:80 default_server;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/ryan/data-registry;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/ryan/data-registry/gunicorn.sock;
    }
}
```
Replacing both instances of `/home/ryan/data-registry/` with the directory where you cloned the git repository

**N.B.** for multisite nginx servers you should add the server name to the file e.g.
```
server_name data.fairdatapipeline.org;
```

Finally restart nginx:
```
sudo systemctl restart nginx
```

You may also need to install and SSL certificate, which can be done using [certbot](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal).
