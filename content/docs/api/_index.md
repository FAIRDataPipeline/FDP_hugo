---
weight: 4
title: "Modelling APIs"
bookCollapseSection: true
---

# API

Every file a model reads or writes through the data pipeline is a *data product*, and any file format can be one. A model script asks the API for the path of an input with `link_read` and for a path to write an output to with `link_write`, then reads or writes the file whole with its own tools. The pipeline records the file, its hash and the code run that used it.

## An end-to-end run

The [R]({{< relref "/docs/api/r" >}}), [Python]({{< relref "/docs/api/python" >}}), [Julia]({{< relref "/docs/api/julia" >}}), [C++]({{< relref "/docs/api/cpp" >}}), [C]({{< relref "/docs/api/c" >}}) and [FORTRAN]({{< relref "/docs/api/fortran" >}}) APIs share the same four functions, and the [Java]({{< relref "/docs/api/java" >}}) API has an equivalent for each (see the table below). `fair run` prepares the code run and then starts the model, whose script uses them as follows:

![An end-to-end run: fair run starts the script, which calls initialise, then link_read for each input, then link_write for each output, optionally raise_issue, and finally finalise](/docs/api/end_to_end_run.svg)

\* The path returned by `link_write` is a temporary path. The file is moved to the data store when `finalise` is called.

† Not every implementation can raise issues yet: the R, Python, Julia and Java APIs can, but the C++, C and FORTRAN APIs cannot.

The names differ slightly between languages:

| Step | R and Python | Julia | C++ | C | FORTRAN | Java |
| --- | --- | --- | --- | --- | --- | --- |
| initialise | `initialise` | `initialise` | `DataPipeline::construct` | `fdp_init` | `%init` | `new Coderun(...)` |
| link_read | `link_read` | `link_read!` | `link_read` | `fdp_link_read` | `%link_read` | `get_dp_for_read` |
| link_write | `link_write` | `link_write!` | `link_write` | `fdp_link_write` | `%link_write` | `get_dp_for_write` |
| raise issue | `raise_issue` and `raise_issue_config`, `raise_issue_script`, `raise_issue_repo` (R); `raise_issue_by_data_product` and other `raise_issue_*` functions (Python) | `raise_issue!` | not yet | not yet | not yet | `raise_issue` |
| finalise | `finalise` | `finalise` | `finalise` | `fdp_finalise` | `%finalise` | `close()`, or the end of a try-with-resources block |
