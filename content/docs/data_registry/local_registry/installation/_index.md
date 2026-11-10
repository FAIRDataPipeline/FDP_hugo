---
title: "Installation"
weight: 1
bookCollapseSection: true
---


# Local FAIR data registry


The documentation for the registry is available [here](https://data.fairdatapipeline.org/docs/), and is the same for the local and remote registry.

## Installation

Installation of the local registry is recommended through the [`FAIR CLI`]({{% ref "/docs/cli/installation" %}}) and can be achieved by the following command:

```
fair registry install
```

The registry is installed in `~/.fair/registry` by default. Options are available to choose the location and the version of the [data registry](https://github.com/FAIRDataPipeline/data-registry) (a tag or branch) to install:

```
fair registry install --directory ~/.fair/my_registry --version v1.5.0
```

Additionally the registry is installed when initialising a FAIR repository, if it is not already installed, using the command:

```
fair init
```

To replace an installed registry with a newer version, see [Upgrading]({{% ref "/docs/cli/installation" %}}#upgrading).

***Note:***

While the local registry will work out of the box, the [```graphviz```](https://www.graphviz.org) package is required to produce graphical [provenance reports]({{% ref "/docs/data_registry/prov_report" %}}#formats), therefore it is recommended that you install `graphviz` prior to installing the registry. To install graphviz please follow the instructions on the [graphviz website](https://www.graphviz.org/download/).

## Running the registry

`fair` commands that use the local registry start it if it is not running, and stop it when the last of them finishes. To start it yourself and keep it running:

```
fair registry start
```

Then navigate to http://localhost:8000 in your browser. The registry's API token is written to `~/.fair/registry/token`. To stop it:

```
fair registry stop
```

`fair registry status` shows where the registry is running and which processes are using it. The port is taken from the CLI configuration (`registries.local.uri` in `~/.fair/cli/cli-config.yaml`), or can be given with `--port`.

The registry's database is a file in its directory, so a registry can only run on one machine at a time. Where the directory is shared between machines, as a home directory on a cluster may be, the registry cannot be used from a machine other than the one running it.

### Default Credentials

By default the local registry creates a superuser for the admin console (http://localhost:8000/admin) with the username: `admin` and the password: `admin`.
