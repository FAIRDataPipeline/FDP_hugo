---
weight: 1
title: "R"
bookCollapseSection: false
---

# rDataPipeline

The `rDataPipeline` package contains functions used to interface with the FAIR Data Pipeline in R.

It is available from [CRAN](https://cran.r-project.org/package=rDataPipeline). To install it, run:

``` R
install.packages("rDataPipeline")
library(rDataPipeline)
```

To install the development version from GitHub instead:

``` R
install.packages("remotes")
remotes::install_github("FAIRDataPipeline/rDataPipeline")
library(rDataPipeline)
```

## Usage

The current release is 0.60.0. A script run by `fair run` finds the working config and submission script in the folder given by the `FDP_CONFIG_DIR` environment variable:

```R
library(rDataPipeline)

config <- file.path(Sys.getenv("FDP_CONFIG_DIR"), "config.yaml")
script <- file.path(Sys.getenv("FDP_CONFIG_DIR"), "script.sh")  # script.bat on Windows
handle <- initialise(config, script)

input_path <- link_read(handle, "SEIRS_model/parameters")
output_path <- link_write(handle, "SEIRS_model/results/model_output/R")
# ... read input_path and write output_path ...

finalise(handle)
```

| Function | Description |
| --- | --- |
| `initialise(config, script)` | Open a code run, returning a handle |
| `link_read(handle, data_product)` | Return the path of an input, recording it as an input of the run |
| `link_write(handle, data_product)` | Return a path to write an output to, recording it as an output of the run |
| `finalise(handle)` | Register the outputs and the code run in the local registry |
| `read_estimate`, `read_distribution`, `write_estimate`, `write_distribution` | Read and write parameters in TOML parameter files |
| `raise_issue`, `raise_issue_config`, `raise_issue_script`, `raise_issue_repo` | Record issues with inputs, outputs, the config, the script or the code |

See the [package documentation][docs] and its [function reference](https://www.fairdatapipeline.org/rDataPipeline/reference/index.html) for full details, and the [SEIRS model example](https://www.fairdatapipeline.org/rSimpleModel/articles/SEIRS.html) and [R simple model](https://github.com/FAIRDataPipeline/rSimpleModel) for a complete example.

## Source code

See the package's [code repo][repo].

[docs]: https://www.fairdatapipeline.org/rDataPipeline/index.html
[repo]: https://github.com/FAIRDataPipeline/rDataPipeline
