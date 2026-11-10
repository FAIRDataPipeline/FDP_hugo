---
weight: 1
title: "Local Registry"
bookCollapseSection: true
---

# Local Registry

The local registry stores metadata about code runs and their inputs and outputs on your own machine, so that models can be run offline. It is installed and managed using the [CLI]({{< relref "/docs/data_registry/local_registry/installation" >}}).

`fair` commands that need the local registry start it when it is not running and stop it when they finish. To keep it running, for example to browse what has been recorded at http://localhost:8000, use `fair registry start` and `fair registry stop`.

The local registry can be synchronised with a [remote registry]({{< relref "/docs/data_registry/remote_registry/" >}}) using [`fair pull` and `fair push`]({{< relref "/docs/cli/cli_commands" >}}).
