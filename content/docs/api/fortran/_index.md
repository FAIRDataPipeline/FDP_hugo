---
weight: 7
title: "FORTRAN"
bookCollapseSection: false
---

# FortranDataPipeline

The `FortranDataPipeline` library contains functions used to interface with the FAIR Data Pipeline in Fortran. It depends on the [C++ API]({{< relref "/docs/api/cpp" >}}), which it calls through the [C API]({{< relref "/docs/api/c" >}}).

## Installation

The library is built using CMake (3.20 or later), and needs a compiler suite that supports C++11 and Fortran 2003. It is recommended that you install [CURL](https://curl.se/libcurl/) first.

```
cmake -Bbuild
cmake --build build
```

To install the library system-wide:

```
cmake --build build --target install
```

It can then be used in other CMake projects:

```cmake
find_package(fdpfort)
target_link_libraries(my_project PRIVATE fdpfort::fdpfort)
```

## Usage

The current release is 0.2.1. The library is the module `fairdatapipeline`, whose `FdpDataPipeline` type has the following procedures. Each takes an optional `err` argument, which is set to `FDP_ERR_NONE` (0) on success:

| Procedure | Description |
| --- | --- |
| `pipeline%init(config_file_path, script_file_path, token, err)` | Initialise the pipeline at the start of a run |
| `pipeline%link_read(data_product, err)` | Return the path of an input data product, recording it as an input |
| `pipeline%link_write(data_product, err)` | Return a path to write an output data product to, recording it as an output |
| `pipeline%finalise(err)` | Record all data products and metadata in the registry at the end of a run |

The module also provides `fdp_log(level, message, err)`, `fdp_set_log_level(level)` and `fdp_get_log_level()` to control and write to the log, with the levels `FDP_LOG_TRACE` to `FDP_LOG_OFF`.

```fortran
use fairdatapipeline, only: FdpDataPipeline
type(FdpDataPipeline) :: pipeline
character(:), allocatable :: input_path, output_path
integer :: err

! config_path and script_path are in the folder given by FDP_CONFIG_DIR,
! and token is the value of FDP_LOCAL_TOKEN
call pipeline%init(config_path, script_path, token, err=err)
input_path = pipeline%link_read("SEIRS_model/parameters", err=err)
output_path = pipeline%link_write("SEIRS_model/results/model_output/fortran", err=err)
! ... read input_path and write output_path ...
call pipeline%finalise()
```

The environment variable `FDP_LOG_LEVEL=[TRACE:DEBUG:INFO:WARN:ERROR:CRITICAL:OFF]` sets the logging level.

See the [Fortran simple model](https://github.com/FAIRDataPipeline/FortranSimpleModel) for a complete example.

## Testing

The tests need a running local registry (`fair registry start`). Build with `-DFDPFORT_BUILD_TESTS=ON` and run the `run-tests` target:

```
cmake -Bbuild . -DFDPFORT_BUILD_TESTS=ON
cmake --build build --target run-tests
```

## Source code

See the package's [code repo](https://github.com/FAIRDataPipeline/FortranDataPipeline).
