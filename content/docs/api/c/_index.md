---
weight: 5
title: "C"
bookCollapseSection: false
---

# C API

The C API is part of the [C++ API (cppDataPipeline)]({{< relref "/docs/api/cpp" >}}) and is built with it. It is declared in the header `fdp/fdp.h`, and is also what the [FORTRAN API]({{< relref "/docs/api/fortran" >}}) uses to call the C++ library.

## Installation

Build and install the C++ library, which includes the C API, as described on the [C++ API]({{< relref "/docs/api/cpp" >}}) page.

## Usage

The pipeline is accessed through a `FdpDataPipeline` struct, which is set up by `fdp_init` and finalised by `fdp_finalise`. Every function returns an `FdpError` code, where `FDP_ERR_NONE` (0) means success.

| Function | Description |
| --- | --- |
| `fdp_init(&pipeline, config_file_path, script_file_path, token)` | Initialise the pipeline. Call once, before any other function |
| `fdp_link_read(pipeline, data_product, path, path_len)` | Get the path of an input data product, recording it as an input of the code run |
| `fdp_link_write(pipeline, data_product, path, path_len)` | Get a path to write an output data product to, recording it as an output of the code run |
| `fdp_finalise(&pipeline)` | Record all data products and metadata in the registry, and free the pipeline |
| `fdp_set_log_level(level)` / `fdp_get_log_level()` / `fdp_log(level, message)` | Control and write to the log |

The config file and script are written by `fair run` to the folder given by the `FDP_CONFIG_DIR` environment variable (`${FDP_CONFIG_DIR}/config.yaml` and `${FDP_CONFIG_DIR}/script.sh`, or `script.bat` on Windows), and the local registry token is given in `FDP_LOCAL_TOKEN`.

```c
#include "fdp/fdp.h"

FdpDataPipeline *pipeline;
char input_path[512], output_path[512];

fdp_init(&pipeline, config_path, script_path, token);
fdp_link_read(pipeline, "SEIRS_model/parameters", input_path, sizeof(input_path));
fdp_link_write(pipeline, "SEIRS_model/results/model_output/c", output_path, sizeof(output_path));
/* ... read input_path and write output_path ... */
fdp_finalise(&pipeline);
```

The log level can also be set with the environment variable `FDP_LOG_LEVEL=[TRACE:DEBUG:INFO:WARN:ERROR:CRITICAL:OFF]`.

## Source code

See the [cppDataPipeline repository](https://github.com/FAIRDataPipeline/cppDataPipeline) and the [package documentation](https://www.fairdatapipeline.org/cppDataPipeline/).
