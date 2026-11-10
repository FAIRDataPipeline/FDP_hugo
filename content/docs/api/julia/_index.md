---
weight: 2
title: "Julia"
bookCollapseSection: false
---

# DataPipeline.jl

The `DataPipeline.jl` package contains functions used to interface with the FAIR Data Pipeline in Julia.

## Installation

The package is registered in Julia's General registry. From the REPL type `]` to enter Pkg mode and run:

``` julia
pkg> add DataPipeline
```

It needs Julia 1.11 or later and [`fair-cli`]({{< relref "/docs/cli/installation" >}}) 0.11.0 or later. If you do not have Python available, `DataPipeline.install_cli!()` installs a copy of the CLI from Julia.

## Usage

The current release is 0.59.1. A model script opens a code run with `DataPipeline.initialise()`, which reads the working config written by `fair run`, and closes it with `DataPipeline.finalise(handle)`. In between, it can:

- get the paths of inputs and outputs with `link_read!` and `link_write!`, the paths of every input whose name matches a pattern with `link_read_files!`, or register data from elsewhere with `link_register!`
- read and write parameters in TOML parameter files with `read_estimate!`, `read_distribution!` and the matching `write_*!` functions
- raise issues on inputs, outputs, the config, the script or the code with `raise_issue!`
- register authors, with their ORCID or ROR ids, with `register_authors!`, and the citations of the sources its inputs come from with `register_citations!`
- build a LaTeX paper with `build_paper!`, so that its PDF records the provenance of every figure and table in it (see [Building a paper](https://www.fairdatapipeline.org/DataPipeline.jl/stable/paper/))

The package also has functions to:

- rehearse a script with `DataPipeline.rehearse` before running it for real, and throw away a rehearsal with `DataPipeline.discard`
- trace where a data product came from with `DataPipeline.provenance`, find what was made from it with `DataPipeline.downstream`, and pack files with their provenance into an [RO-Crate]({{< relref "/docs/data_registry/ro_crate" >}}) with `DataPipeline.crate` (see [Tracing provenance](https://www.fairdatapipeline.org/DataPipeline.jl/stable/provenance/))
- find out what the registry knows about a file or a git commit with `DataPipeline.identify`, check the data store against the registry with `DataPipeline.check_store`, and register a code release with `DataPipeline.register_release!`

The `fair"..."` string macro runs the `fair` command-line tool from Julia.

See the [package documentation][docs] for instructions and examples, including the [FAIR data pipeline manual](https://www.fairdatapipeline.org/DataPipeline.jl/stable/fdp_manual/) and the [SEIRS model example](https://www.fairdatapipeline.org/DataPipeline.jl/stable/SEIRS/).

## Source code

See the package's [code repo][repo].

[docs]: https://www.fairdatapipeline.org/DataPipeline.jl/stable/
[repo]: https://github.com/FAIRDataPipeline/DataPipeline.jl
