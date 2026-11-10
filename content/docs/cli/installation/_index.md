---
weight: 1
title: "FAIR CLI Installation"
bookCollapseSection: false
---

# Installation

The FAIR CLI is available to install as a python package, and supports [![PyPI - Python Version](https://img.shields.io/pypi/pyversions/fair-cli)](https://pypi.org/project/fair-cli/) (Python 3.10 or later).

It is recommended to install the FAIR CLI in a virtual environment such as a [venv](https://docs.python.org/3/library/venv.html), [conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html) or [uv](https://docs.astral.sh/uv/) environment. The local registry is installed with the same Python as the CLI, in an environment of its own.

## Create and activate a virtual environment

### venv
[venv instructions](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments)

### Conda
[Conda instructions](https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html#creating-environments)

### uv
[uv instructions](https://docs.astral.sh/uv/pip/environments/#creating-a-virtual-environment) ([install uv](https://docs.astral.sh/uv/getting-started/installation/) first)

With uv, install the CLI into the environment using `uv pip install fair-cli` in place of `pip install fair-cli`.

**Note you will need to ensure the environment is activated each time you use the FAIR CLI**

## Install from pypi using pip (recommended)

```
pip3 install fair-cli
```

***Note:***
On windows the command may be:
```
pip install fair-cli
```

## Install the local registry

The CLI needs a [local registry]({{< relref "/docs/data_registry/local_registry" >}}) to record metadata. Install it with:

```
fair registry install
```

[Graphviz](https://graphviz.org/download/) is needed to draw provenance reports, so install it before the registry if you want them.

## Tab completion (optional)

To enable tab completion, add the completion script to your shell:

### Bash
```
_FAIR_COMPLETE=bash_source fair > ~/.config/.fair-complete.bash
echo '. ~/.config/.fair-complete.bash' >> ~/.bashrc
```

### zsh
```
_FAIR_COMPLETE=zsh_source fair > ~/.fair-complete.zsh
echo '. ~/.fair-complete.zsh' >> ~/.zshrc
```

## Upgrading

The CLI and the local registry are installed separately (the CLI by `pip`, the registry by the CLI), so upgrading one does not upgrade the other.

### The CLI

To upgrade to the newest release, or install a particular one:

```
pip install --upgrade fair-cli
pip install fair-cli==0.11.0
```

To install a branch, tag or commit of the [FAIR-CLI repository](https://github.com/FAIRDataPipeline/FAIR-CLI):

```
pip install git+https://github.com/FAIRDataPipeline/FAIR-CLI@<branch>
```

A branch keeps the same version number between commits, so add `--force-reinstall --no-deps` to update an installed branch.

### The local registry

To replace the local registry with its newest release:

```
fair registry stop
fair registry install --force
fair registry start
```

Add `--version <tag or branch>` to install a particular version of the [data registry](https://github.com/FAIRDataPipeline/data-registry). With `--force` the old registry is kept until the new one is built, and is put back if the install fails.

Reinstalling the registry replaces its database, so every record in the local registry is lost (files in the data store are kept). Records that were pushed to a remote registry can be pulled back. After a reinstall, run `fair init` again in each project (`fair-cli` 0.10.2 or later) to register its user in the new registry.

## Uninstalling

```
fair purge --all
pip uninstall fair-cli
```

`fair purge --all` removes the `.fair` folder of the current project and the whole of `~/.fair`: the local registry, the default data store and the CLI's configuration. Leave it out to keep them.
