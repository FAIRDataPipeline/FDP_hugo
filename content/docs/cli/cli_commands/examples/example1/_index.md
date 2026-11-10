---
weight: 4
title: "CLI Examples"
---

# CLI Examples

This page gives a full working example of a **user-written** *config.yaml* file, describes the **working** *config.yaml* that `fair run` generates from it, and shows submission scripts that use it. Note that **the language APIs take the working config file as their input**, not the user-written one.

The example is the SEIRS epidemiological model used by the simple model repositories ([Python](https://github.com/FAIRDataPipeline/pySimpleModel), [R](https://github.com/FAIRDataPipeline/rSimpleModel), [Julia](https://github.com/FAIRDataPipeline/DataPipeline.jl/tree/main/examples/fdp), [Java](https://github.com/FAIRDataPipeline/javaSimpleModel), [C++](https://github.com/FAIRDataPipeline/cppSimpleModel), [FORTRAN](https://github.com/FAIRDataPipeline/FortranSimpleModel)). It registers a file of model parameters from outside the pipeline, runs the model, and records the parameters, the model results and a plot in the pipeline.

From the root of the git repository holding the model, run:

```bash
fair init
fair pull config.yaml
fair run config.yaml
```

and, to share the results on a remote registry:

```bash
fair status
fair add <namespace>:SEIRS_model/results/model_output/python@v0.0.1
fair push
```

## User-written *config.yaml*

```yaml
run_metadata:
  description: SEIRS Model python
  default_input_namespace: PSU
  script: python simpleModel/ext/SEIRSModelRun.py

register:
- namespace: PSU
  full_name: Pennsylvania State University
  website: https://ror.org/04p491231

- external_object: SEIRS_model/parameters
  namespace_name: PSU
  root: https://raw.githubusercontent.com/
  path: FAIRDataPipeline/pySimpleModel/main/simpleModel/ext/static_params_SEIRS.csv
  title: Static parameters of the model
  description: Static parameters of the model
  identifier: https://doi.org/10.1038/s41592-020-0856-2
  file_type: csv
  release_date: 2021-09-20T12:00
  version: "1.0.0"
  primary: false

write:
- data_product: SEIRS_model/results/model_output/python
  description: SEIRS model results
  file_type: csv

- data_product: SEIRS_model/results/figure/python
  description: SEIRS output plot
  file_type: png
```

### `run_metadata:`

- `script:` the commands to run, or `script_path:` the path of a script to run. `shell:` chooses the program that runs it (see [Choosing a shell]({{< relref "/docs/cli/cli_commands#choosing-a-shell" >}})).
- `default_input_namespace:` and `default_output_namespace:` the namespaces used for entries that do not give one.
- `description:` a description of the code run.
- `public:` whether outputs are public (default `true`).
- The local repository, the registry URLs (`local_data_registry_url`, `remote_data_registry_url`), the data store (`write_data_store`) and any namespace not given here are filled in from the CLI's configuration, set by `fair init`, so they are usually left out.

### `register:`

Data from outside the pipeline, which `fair pull` downloads and registers.

- A `namespace:` entry registers a namespace (`full_name:` and optionally `website:`).
- An `external_object:` entry needs `namespace_name:`, `root:` and `path:` (the file's URL is `root` followed by `path`), `title:`, `file_type:`, `release_date:`, `version:` and `primary:` (whether this is a primary source), and either `identifier:` (*e.g.* a DOI) or both `unique_name:` and `alternate_identifier_type:`. `description:` is optional.
- Write `version` in quotes, so that YAML reads it as text.

### `read:`

Data products already in the registry that the run reads, given by `data_product:` and optionally a `use:` section with `version:` and `namespace:`. Objects listed in `register:` are added to `read:` by `fair run`, so they do not need to be listed here.

### `write:`

Data products that the run writes. Each entry needs `data_product:` and `file_type:`, and may give `description:`, `public:` and a `use:` section. A `use: data_product:` lets the script refer to an output by a short name while it is registered under a longer one, as the Julia example does:

```yaml
write:
- data_product: model_output
  description: SEIRS model results
  file_type: csv
  use:
    data_product: SEIRS_model/results/model_output/julia
```

Versions can be given with [template variables]({{< relref "/docs/cli/cli_commands#template-variables" >}}), *e.g.* `version: 0.${{DATE}}.0`. Without a version, a new data product is written as `0.0.1`, and each later run increments the patch number.

## Working *config.yaml*

`fair run` validates the user-written config, then writes the working *config.yaml* and the submission script to a new folder in the data store (*<data_store>/coderun/\<date>T\<time>/*), and runs the script with `FDP_CONFIG_DIR` set to that folder. In the working config:

- `run_metadata:` is completed with the local repository (`local_repo`), its latest commit (`latest_commit`), its remote URL (`remote_repo`), the registry URLs, the data store, the default namespaces and `public`
- each `register:` entry becomes a `read:` entry, and `register:` is removed
- every `read:` and `write:` entry has a `use:` section giving the `data_product`, `namespace` and exact `version` to use
- template variables and wildcards are replaced with their values
- every `write:` entry has `public:` set

For example, the `run_metadata:` of the working config for the example above looks like:

```yaml
run_metadata:
  description: SEIRS Model python
  script: python simpleModel/ext/SEIRSModelRun.py
  default_input_namespace: PSU
  default_output_namespace: <your namespace>
  local_repo: /path/to/pySimpleModel
  latest_commit: <commit hash>
  remote_repo: https://github.com/FAIRDataPipeline/pySimpleModel
  local_data_registry_url: http://127.0.0.1:8000/api/
  remote_data_registry_url: https://data.fairdatapipeline.org/api/
  write_data_store: /home/<user>/.fair/data/
  public: true
```

To see the full working config of a run, look in its folder in the data store, or prepare one without running it using `fair run --rehearse config.yaml`.

## Submission scripts

The script reads the location of the working config and submission script from `FDP_CONFIG_DIR`, and the local registry token from `FDP_LOCAL_TOKEN`. The submission script is *script.sh* (*script.bat* on Windows). Every API follows the same pattern:

1. `initialise` reads the working config and opens a code run, registering the config, the submission script and the code repository.
2. `link_read` returns the path of an input in the local data store, and `link_write` returns a path to write an output to. Each is recorded against the code run.
3. `finalise` renames output files with their hash, and records the outputs, any issues raised, and the code run in the local registry.

### Python

From [pySimpleModel](https://github.com/FAIRDataPipeline/pySimpleModel/blob/main/simpleModel/ext/SEIRSModelRun.py), using [pyDataPipeline]({{< relref "/docs/api/python" >}}):

```python
import os
import platform
import data_pipeline_api as pipeline

token = os.environ["FDP_LOCAL_TOKEN"]
config_dir = os.environ["FDP_CONFIG_DIR"]
config = os.path.join(config_dir, "config.yaml")
script = os.path.join(config_dir, "script.bat" if platform.system() == "Windows" else "script.sh")

handle = pipeline.initialise(token, config, script)

initial_parameters = pipeline.link_read(handle, "SEIRS_model/parameters")
model_output = pipeline.link_write(handle, "SEIRS_model/results/model_output/python")
model_plot = pipeline.link_write(handle, "SEIRS_model/results/figure/python")

# ... run the model, reading initial_parameters and writing model_output and model_plot ...

pipeline.finalise(token, handle)
```

### R

From [rSimpleModel](https://github.com/FAIRDataPipeline/rSimpleModel/blob/main/inst/extdata/SEIRS.R), using [rDataPipeline]({{< relref "/docs/api/r" >}}):

```R
library(rDataPipeline)

conf.dir <- Sys.getenv("FDP_CONFIG_DIR")
config <- file.path(conf.dir, "config.yaml")
script <- file.path(conf.dir, "script.sh")
if (.Platform$OS.type != "unix") {
  script <- file.path(conf.dir, "script.bat")
}
handle <- initialise(config, script)

params <- read.csv(link_read(handle, "SEIRS_model/parameters"))

# ... run the model ...

write.csv(results, link_write(handle, "model_output"), row.names = FALSE)

finalise(handle)
```

rDataPipeline can also read and write individual parameters in TOML parameter files, with `read_estimate()`, `write_estimate()` and similar functions, and raise issues with `raise_issue()`.

### Julia

From the [DataPipeline.jl example](https://github.com/FAIRDataPipeline/DataPipeline.jl/blob/main/examples/fdp/seirs_sim.jl), using [DataPipeline.jl]({{< relref "/docs/api/julia" >}}). `DataPipeline.initialise()` finds the working config, script and token from the environment itself:

```julia
using DataPipeline
using CSV
using DataFrames

handle = DataPipeline.initialise()

path = link_read!(handle, "SEIRS_model/parameters")
static_params = CSV.read(path, DataFrames.DataFrame)

# ... run the model ...

path = link_write!(handle, "model_output")
CSV.write(path, results)

DataPipeline.finalise(handle)
```

### Java, C++ and FORTRAN

See [javaSimpleModel](https://github.com/FAIRDataPipeline/javaSimpleModel) and the [Java API]({{< relref "/docs/api/java/run" >}}), [cppSimpleModel](https://github.com/FAIRDataPipeline/cppSimpleModel) and the [C++ API]({{< relref "/docs/api/cpp" >}}), and [FortranSimpleModel](https://github.com/FAIRDataPipeline/FortranSimpleModel) and the [FORTRAN API]({{< relref "/docs/api/fortran" >}}).
