---
weight: 6
title: "Repository Status"
bookCollapseSection: false
---

Work in progress. All blank cells are TBDs. Idea is not to duplicate the software checklist or other information held in repositories, but to summarise it in one place.

# CI Dashboard

| Repository | CI Status | Coverage | Default branch | Static Analysis |
| --- | --- | --- | --- | --- |
| FAIR-CLI | [![FAIR Data Pipeline CLI](https://github.com/FAIRDataPipeline/FAIR-CLI/actions/workflows/fair-cli.yaml/badge.svg?branch=main)](https://github.com/FAIRDataPipeline/FAIR-CLI/actions/workflows/fair-cli.yaml) | [![codecov](https://codecov.io/gh/FAIRDataPipeline/FAIR-CLI/branch/main/graph/badge.svg)](https://codecov.io/gh/FAIRDataPipeline/FAIR-CLI) | `main` | [![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=FAIRDataPipeline_FAIR-CLI&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=FAIRDataPipeline_FAIR-CLI) |
| data-registry | [![FAIR Data Registry](https://github.com/FAIRDataPipeline/data-registry/actions/workflows/fair-data-registry.yaml/badge.svg?branch=main)](https://github.com/FAIRDataPipeline/data-registry/actions/workflows/fair-data-registry.yaml) | [![codecov](https://codecov.io/gh/FAIRDataPipeline/data-registry/branch/main/graph/badge.svg)](https://codecov.io/gh/FAIRDataPipeline/data-registry) | `main` | **unknown** |
| cppDataPipeline| [![FDP C++ API](https://github.com/FAIRDataPipeline/cppDataPipeline/actions/workflows/fdp_cpp_api.yaml/badge.svg)](https://github.com/FAIRDataPipeline/cppDataPipeline/actions/workflows/fdp_cpp_api.yaml) | [![Coverage](https://sonarcloud.io/api/project_badges/measure?project=FAIRDataPipeline_FDP-Cpp-API&metric=coverage)](https://sonarcloud.io/summary/new_code?id=FAIRDataPipeline_FDP-Cpp-API) | `main` | [![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=FAIRDataPipeline_FDP-Cpp-API&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=FAIRDataPipeline_FDP-Cpp-API) |
| FortranDataPipeline | [![FDP Fortran API](https://github.com/FAIRDataPipeline/FortranDataPipeline/actions/workflows/test.yml/badge.svg)](https://github.com/FAIRDataPipeline/FortranDataPipeline/actions/workflows/test.yml) | | `main` | |
| DataPipeline.jl | [![Package Testing](https://github.com/FAIRDataPipeline/DataPipeline.jl/actions/workflows/testing.yaml/badge.svg)](https://github.com/FAIRDataPipeline/DataPipeline.jl/actions/workflows/testing.yaml) | [![codecov](https://codecov.io/gh/FAIRDataPipeline/DataPipeline.jl/branch/main/graph/badge.svg)](https://codecov.io/gh/FAIRDataPipeline/DataPipeline.jl) | `main` | **unknown** |
| javaDataPipeline | [![test with reg](https://github.com/FAIRDataPipeline/javaDataPipeline/actions/workflows/build-test-with-registry.yml/badge.svg)](https://github.com/FAIRDataPipeline/javaDataPipeline/actions/workflows/build-test-with-registry.yml) | [![codecov](https://codecov.io/gh/FAIRDataPipeline/javaDataPipeline/branch/master/graph/badge.svg?token=WHX17OYLBo)](https://codecov.io/gh/FAIRDataPipeline/javaDataPipeline) | `master` | Code style checked with Spotless |
| pyDataPipeline | [![pyDataPipeline](https://github.com/FAIRDataPipeline/pyDataPipeline/actions/workflows/pyDataPipeline-1.yaml/badge.svg?branch=main)](https://github.com/FAIRDataPipeline/pyDataPipeline/actions/workflows/pyDataPipeline-1.yaml) | [![codecov](https://codecov.io/gh/FAIRDataPipeline/pyDataPipeline/branch/main/graph/badge.svg)](https://codecov.io/gh/FAIRDataPipeline/pyDataPipeline) | `main` | **unknown** |
| rDataPipeline | [![rDataPipeline](https://github.com/FAIRDataPipeline/rDataPipeline/actions/workflows/test-build.yaml/badge.svg)](https://github.com/FAIRDataPipeline/rDataPipeline/actions/workflows/test-build.yaml) | [![codecov](https://codecov.io/gh/FAIRDataPipeline/rDataPipeline/branch/main/graph/badge.svg)](https://codecov.io/gh/FAIRDataPipeline/rDataPipeline) | `main` | **unknown** |
| cppSimpleModel | [![cppSimpleModel](https://github.com/FAIRDataPipeline/cppSimpleModel/actions/workflows/cpp_simple_model.yaml/badge.svg)](https://github.com/FAIRDataPipeline/cppSimpleModel/actions/workflows/cpp_simple_model.yaml) | NA | `main` | NA |
| FortranSimpleModel | Tested by the [FortranDataPipeline CI](https://github.com/FAIRDataPipeline/FortranDataPipeline/actions/workflows/simple_model.yml) | NA | `main` | NA |
| Julia example | Part of [DataPipeline.jl](https://github.com/FAIRDataPipeline/DataPipeline.jl/tree/main/examples) | NA | `main` | NA |
| javaSimpleModel | ![build](https://github.com/FAIRDataPipeline/javaSimpleModel/actions/workflows/gradle-build.yml/badge.svg) ![run w fair cli](https://github.com/FAIRDataPipeline/javaSimpleModel/actions/workflows/runWithFairCli.yml/badge.svg) | NA | `master` | NA |
| pySimpleModel | ![build](https://github.com/FAIRDataPipeline/pySimpleModel/actions/workflows/pySimpleModel.yaml/badge.svg) | NA | `main` | NA |
| rSimpleModel | ![build](https://github.com/FAIRDataPipeline/rSimpleModel/actions/workflows/test-build.yaml/badge.svg) | NA | `main` | NA |

Note: static analysis may not be available or appropriate for all languages.

# Documentation

| Repository | Remarks |
| --- | --- |
| FAIR-CLI | Docs in readme and in `docs` folder, docstrings not currently used to build docs. User docs on this site: [FAIR CLI]({{< relref "/docs/cli" >}}) |
| data-registry | User docs on this site: [FAIR Data Registry]({{< relref "/docs/data_registry" >}}). Registry docs at <https://data.fairdatapipeline.org/docs/>, dev docs in docs folder of repo. |
| cppDataPipeline | Automated docs built to website <https://www.fairdatapipeline.org/cppDataPipeline/>, also [C++]({{< relref "/docs/api/cpp" >}}) and [C]({{< relref "/docs/api/c" >}}) |
| FortranDataPipeline | <https://github.com/FAIRDataPipeline/FortranDataPipeline#readme>, also [FORTRAN]({{< relref "/docs/api/fortran" >}}) |
| DataPipeline.jl | Automated docs built to website <https://www.fairdatapipeline.org/DataPipeline.jl/stable/>, also [Julia]({{< relref "/docs/api/julia" >}}) |
| javaDataPipeline |  Automated docs built to website <https://www.fairdatapipeline.org/javaDataPipeline/>, also user docs [Java]({{< relref "/docs/api/java" >}}) |
| pyDataPipeline | Automated docs built to website <https://www.fairdatapipeline.org/pyDataPipeline/>, also [Python]({{< relref "/docs/api/python" >}}) |
| rDataPipeline | Automated docs built to website <https://www.fairdatapipeline.org/rDataPipeline/>, also [R]({{< relref "/docs/api/r" >}}) |
| cppSimpleModel | <https://github.com/FAIRDataPipeline/cppSimpleModel#readme> |
| FortranSimpleModel | <https://github.com/FAIRDataPipeline/FortranSimpleModel#readme> |
| Julia example | <https://www.fairdatapipeline.org/DataPipeline.jl/stable/SEIRS/> |
| javaSimpleModel |<https://github.com/FAIRDataPipeline/javaSimpleModel#readme> |
| pySimpleModel | <https://github.com/FAIRDataPipeline/pySimpleModel#readme> |
| rSimpleModel | <https://github.com/FAIRDataPipeline/rSimpleModel#readme> |
| FDPTutorial | <https://github.com/FAIRDataPipeline/FDPTutorial#readme> (Docker + Jupyter walkthrough) |

# Status

{{<repo_stat_table>}}
