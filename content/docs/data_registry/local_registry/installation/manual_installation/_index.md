---
weight: 1
title: "Manual Installation"
bookCollapseSection: false
bookHidden: true
---

## Installation

There are a few alternative ways to install a local FAIR data registry and we describe the different options below.
### Dependencies

The registry relies on the [```graphviz```](https://www.graphviz.org) package to produce the [schema visualisation](/docs/data_registry/schema/#schema-diagram) and the [provenance report](/docs/data_registry/prov_report), so you will need to follow [graphviz installation process](https://www.graphviz.org/download/) for your system before initialising a local registry.

#### Windows Dependencies

To install and run the local registry on windows the following dependencies are required:

  - [Chocolatey](https://chocolatey.org/)
    
Once Chocolatey is installed the following dependencies can be installed using chocolatey:
    
      - [Python 3.10 or later](https://community.chocolatey.org/packages/python)
      - [Curl](https://community.chocolatey.org/packages/curl)
      - [Git](https://community.chocolatey.org/packages/git)

### Install local registry (Linux / MAC OS)

To initialise a local registry, run the following command from your terminal:

```
/bin/bash -c "$(curl -fsSL https://data.fairdatapipeline.org/static/localregistry.sh)"
```

This will install the registry and all the related files will be stored in `~/.fair`.

To run the server, run the script:

```
~/.fair/registry/scripts/start_fair_registry
```

Then, navigate to http://localhost:8000 in your browser to check that the server is up and running. A token will be automatically generated in `~/.fair/registry/token`.

To stop the server, run the script:

```
~/.fair/registry/scripts/stop_fair_registry
```

#### Install specific branch

If you need to install a specific branch from the [registry repository](https://github.com/FAIRDataPipeline/data-registry), you can replace `<branch_name>` with the branch in question in the following command:

```
curl -fsSL https://data.fairdatapipeline.org/static/localregistry.sh | /bin/bash -s -- -b <branch_name>
```

### Install local registry (Windows)

To initialise a local registry on windows, run the following commands from command prompt

```
curl https://data.fairdatapipeline.org/static/localregistry.bat > localregistry.bat

localregistry.bat
```

This will install the registry and all the related files will be stored in `C:\Users\<username>\.fair`

To run the server, run the `C:\Users\<username>\.fair\registry\scripts\start_fair_registry_windows.bat` script, this will spawn the server in a new window.
Navigate to http://localhost:8000 in your browser to check the server is up and running. A token will be automatically generated in `C:\Users\<username>\.fair\registry\token`.

To stop the server switch to the server window and press control + break or run: `C:\Users\<username>\.fair\registry\scripts\stop_fair_registry_windows.bat`.

### Default Credentials

By default the local registry creates a superuser for the admin console with the username: `admin` and the password: `admin`

### Using Vagrant in a local VM

An alternative to run the local registry without worrying about dependencies is to rely on [Vagrant](https://www.vagrantup.com/) and a virtualisation engine such as [VirtualBox](https://www.virtualbox.org/).

The FAIR data registry codebase provides a [Vagrantfile](https://github.com/FAIRDataPipeline/data-registry/blob/main/Vagrantfile) with the details on how to configure and provision a local virtual machine to run the data registry.

So, the steps to follow are:

- Clone the repository: 
  
  ```
  git clone https://github.com/FAIRDataPipeline/data-registry.git
  ```
- Run Vagrant:
  
  ```
  vagrant up
  ```

and your local FAIR data registry should be running at ```http://localhost:8000/```.

If you then need to get into the VM for managing the local registry, you can use the following commands:
 ```
 vagrant ssh
 sudo su
 cd /code/data-registry/
 ```
Then, to stop the data registry server you can use the following command:
 ```
 scripts/stop_fair_registry
 ```
And to start it again, you can use:
 ```
 scripts/start_fair_registry_vagrant
 ```

If you need to access the python/Django commands, still within the VM, you can activate the python virtual environment, add a Django environment variable and check the ```manage.py``` options as follows:
 ```
 . venv/bin/activate
 export DJANGO_SETTINGS_MODULE=drams.vagrant-settings
 python manage.py --help
 ```

 For example, you can use the following command to add some example data:
 ```
 python manage.py add_example_data
 ```

 Note that if you want to remove the VM, you can run the command:

 ```
  vagrant destroy
  ```