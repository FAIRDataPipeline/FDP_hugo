---
weight: 6
title: "C++"
bookCollapseSection: false
---

# cppDataPipeline

The `cppDataPipeline` library contains functions used to interface with the FAIR Data Pipeline in C++. It also provides the [C API]({{< relref "/docs/api/c" >}}).

## Building

The library is built using CMake (3.13 or later) and requires a C++11 compatible compiler. It is recommended that you install [CURL](https://curl.se/libcurl/) first.

```
cmake -Bbuild
cmake --build build
```

For multi-config generators such as Visual Studio, Xcode or Ninja Multi-Config, the build type must be set to `Release`:

```
cmake --build build --config=Release
```

To install the library system-wide (this requires Boost, or build a shared library with `-DBUILD_SHARED_LIBS=ON`):

```
cmake --build build --target install
```

It can then be used in other CMake projects:

```cmake
find_package(fdpapi)
target_link_libraries(my_project PRIVATE fdpapi::fdpapi)
```

To build and run the unit tests, configure with `-DFDPAPI_BUILD_TESTS=ON` and run `./build/bin/fdpapi-tests`. The tests need a running local registry (`fair registry start`).

## Usage

The current release is 0.2.7. The library is declared in the header `fdp/fdp.hxx`, in the namespace `FairDataPipeline`. A pipeline is created with `DataPipeline::construct`, which returns a shared pointer, and provides the methods `link_read`, `link_write` and `finalise`:

```cpp
#include "fdp/fdp.hxx"

namespace FDP = FairDataPipeline;

// config_path and script_path are in the folder given by FDP_CONFIG_DIR,
// and token is the value of FDP_LOCAL_TOKEN
FDP::DataPipeline::sptr pipeline = FDP::DataPipeline::construct(config_path, script_path, token);

std::string input = "SEIRS_model/parameters";
std::string output = "SEIRS_model/results/model_output/cpp";
std::string input_path = pipeline->link_read(input);
std::string output_path = pipeline->link_write(output);
// ... read input_path and write output_path ...

pipeline->finalise();
```

The environment variable `FDP_LOG_LEVEL=[TRACE:DEBUG:INFO:WARN:ERROR:CRITICAL:OFF]` sets the logging level.

See the [package documentation][docs] for instructions and examples, and the [C++ simple model](https://github.com/FAIRDataPipeline/cppSimpleModel) for a complete example.

## Source code

See the package's [code repo][repo].

[docs]: https://www.fairdatapipeline.org/cppDataPipeline/
[repo]: https://github.com/FAIRDataPipeline/cppDataPipeline
