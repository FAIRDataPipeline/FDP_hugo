---
weight: 3
title: "Python"
bookCollapseSection: false
---

# pyDataPipeline

The `pyDataPipeline` python package contains functions used to interface with the FAIR Data Pipeline in python.

The current release is 0.9.1. It requires Python 3.10 or later ([![PyPI - Python Version](https://img.shields.io/pypi/pyversions/data-pipeline-api)](https://pypi.org/project/data-pipeline-api/)) and can be installed using [pip](https://pypi.org/project/data-pipeline-api/):

```
pip3 install data-pipeline-api
```

***Note:***
On windows the command may be:
```
pip install data-pipeline-api
```

## Usage

The package is imported as `data_pipeline_api`. A script run by `fair run` finds the working config, submission script and local registry token through environment variables set by the CLI:

```python
import os
import data_pipeline_api as pipeline

token = os.environ["FDP_LOCAL_TOKEN"]
config_dir = os.environ["FDP_CONFIG_DIR"]
config = os.path.join(config_dir, "config.yaml")
script = os.path.join(config_dir, "script.sh")  # script.bat on Windows

handle = pipeline.initialise(token, config, script)

input_path = pipeline.link_read(handle, "SEIRS_model/parameters")
output_path = pipeline.link_write(handle, "SEIRS_model/results/model_output/python")
# ... read input_path and write output_path ...

pipeline.finalise(token, handle)
```

| Function | Description |
| --- | --- |
| `initialise(token, config, script)` | Open a code run, returning a handle |
| `link_read(handle, data_product)` | Return the path of an input, recording it as an input of the run |
| `link_write(handle, data_product)` | Return a path to write an output to, recording it as an output of the run |
| `finalise(token, handle)` | Register the outputs and the code run in the local registry |
| `raise_issue_by_data_product`, `raise_issue_by_index`, `raise_issue_by_existing_data_product` | Record issues with inputs and outputs |
| `raise_issue_with_config`, `raise_issue_with_submission_script`, `raise_issue_with_github_repo`, or `raise_issue_by_type` with `"config"`, `"submission_script"` or `"github_repo"` | Record issues with the config, the script or the code |

See the [package documentation][docs] for full details and the [SEIRS model example](https://www.fairdatapipeline.org/pyDataPipeline/examples/SEIRS.html), and the [Python simple model](https://github.com/FAIRDataPipeline/pySimpleModel) for a complete example.

## Source code

See the package's [code repo][repo].

[docs]: https://www.fairdatapipeline.org/pyDataPipeline/
[repo]: https://github.com/FAIRDataPipeline/pyDataPipeline
