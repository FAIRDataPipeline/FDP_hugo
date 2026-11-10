---
weight: 1
title: "Quick Start"
bookCollapseSection: false
---

The FAIR Data Pipeline needs three components to be installed on your computer in order to run. These are:

- [Command Line Interface (CLI)]({{< relref "/docs/cli/installation" >}})
- [(Local) Data Registry]({{< relref "/docs/data_registry/local_registry" >}})
- [A Modelling API (in whatever programming language you want to use)]({{< relref "/docs/api" >}})

## Try it without installing anything

The [FDP Tutorial](https://github.com/FAIRDataPipeline/FDPTutorial) runs in a Docker container with the CLI, the local registry and the example models already installed, and walks through the pipeline in Jupyter notebooks:

```
docker run -p 8000:8000 -p 8888:8888 ghcr.io/fairdatapipeline/fdptutorial:latest
```

## Prerequisites

The CLI is installed using `pip` (the package installer for Python), so you will need Python 3.10 or later (![PyPI - Python Version](https://img.shields.io/pypi/pyversions/fair-cli)) and [git](https://git-scm.com/downloads). You will also need to [install Graphviz](https://graphviz.org/download/) if you would like to view provenance reports in the registry's web interface.

## Install the Command Line Interface (CLI)

It is recommended to install the CLI in a virtual environment:

```
pip install fair-cli
```

See [CLI Installation]({{< relref "/docs/cli/installation" >}}) for more details.

## Install the (Local) Data Registry

```
fair registry install
```

See [Local Registry Installation]({{< relref "/docs/data_registry/local_registry/installation" >}}) for more details.

## Select and install a Modelling API

| Language | Install | Details |
| --- | --- | --- |
| C | Built with the C++ API | [C API]({{< relref "/docs/api/c" >}}) |
| C++ | Build with CMake | [C++ API]({{< relref "/docs/api/cpp" >}}) |
| FORTRAN | Build with CMake | [FORTRAN API]({{< relref "/docs/api/fortran" >}}) |
| Java | `org.fairdatapipeline:api` on Maven Central | [Java API]({{< relref "/docs/api/java" >}}) |
| Julia | `pkg> add DataPipeline` | [Julia API]({{< relref "/docs/api/julia" >}}) |
| Python | `pip install data-pipeline-api` | [Python API]({{< relref "/docs/api/python" >}}) |
| R | `install.packages("rDataPipeline")` | [R API]({{< relref "/docs/api/r" >}}) |

## Try out an example

Example models (a simple SEIRS epidemiological model) using the FAIR Data Pipeline are provided in each of the supported languages. Instructions to install and run these examples can be found in their respective GitHub repositories:

- [C++](https://github.com/FAIRDataPipeline/cppSimpleModel)
- [FORTRAN](https://github.com/FAIRDataPipeline/FortranSimpleModel)
- [Java](https://github.com/FAIRDataPipeline/javaSimpleModel)
- Julia (examples are included with the [Julia Modelling API](https://github.com/FAIRDataPipeline/DataPipeline.jl/tree/main/examples))
- [Python](https://github.com/FAIRDataPipeline/pySimpleModel)
- [R](https://github.com/FAIRDataPipeline/rSimpleModel)

Each example is run the same way: clone the repository, then from inside it run

```
fair init
fair pull <path to config.yaml>
fair run <path to config.yaml>
```

Then start the registry with `fair registry start` and visit http://localhost:8000 to see the inputs and outputs recorded for the run.
