---
title: "Introduction"
type: docs
---

# Introduction

The FAIR Data Pipeline is intended to enable tracking of provenance of [FAIR](https://doi.org/10.1038/sdata.2016.18) (findable, accessible and interoperable) data used in epidemiological modelling. Pipeline APIs written in C++, Java, Julia, Python and R can be called by modelling software for data ingestion. These interact with a local relational database storing metadata and the local filesystem, and are configured using a yaml file associated with the model run. Local files and metadata can be synchronised with a remote registry via a command line tool (`fair`).

The key benefits of using the FAIR Data Pipeline are:

- Data recorded in a FAIR fashion (metadata on all data and code open and available for inspection)
- Provenance tracing allows model outputs to be traced to inputs and modelling code
- Multiple language support
- Designed to run on a broad range of platforms (including HPC, inside Safe Havens)
- Designed to be set up and completed online (to down-/up-load data) and run offline (Safe Havens will require this)
- Open metadata provides knowledge of or access to shared central data for specific domains (e.g. COVID-19 epidemiological modelling)

## Running Models

To use the FAIR Data Pipeline with a piece of modelling software, you must add a language specific Pipeline API as a dependency and interact with data registered in the pipeline via the methods it presents. Each model run must be configured using a [`config.yml`](docs/interface/_index.md) file which specifies inputs and outputs by metadata.

{{<mermaid align="left">}}
graph LR;
    subgraph CLI
        fair
    end
    subgraph Local API
        API[Pipeline API]
        CY[config.yml]
    end
    subgraph localhost
        LR[Registry]
        FS[File Store]
    end
    subgraph Model
        MC[Model code]
    end
 
    fair --> CY
    CY --> API

    fair --> MC

    MC --> |read/write/link_*| API
    API --> |read/write/link_*| LR

    LR --> |read/link_*| API
    API --> |read/link_*| MC

    API --> |write_*| FS
    FS  --> |read_*| API

    MC --> |from link_write| FS
    FS --> |from link_read| MC

{{< /mermaid >}}

## Getting data

The command line utility `fair` is used to download and upload data and metadata required for and produced by model runs.

{{<mermaid align="left">}}
graph LR;
    subgraph Remote
        RR
        OS
        URI
    end
    subgraph Local
        LR
        FS
    end

    RR[Remote Registry]-->|fair pull| LR[Local Registry]
    LR-->|fair push| RR

    RR-->OS(Managed Object Store)
    RR-->URI(Arbitrary URI)
    LR-->FS(Local Filesystem)

    OS-->|fair pull| FS
    URI-->|fair pull| FS
    FS-->|fair push| OS
    FS-->|fair push| URI

{{< /mermaid >}}
