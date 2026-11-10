---
weight: 2
title: "CLI Local Command(s)"
bookCollapseSection: false
---

# `fair` CLI Local Command(s)

By default the CLI expects a [remote registry]({{< relref "/docs/data_registry/remote_registry/" >}}), however it can run without one using the `--local` flag, which is useful on closed systems.

N.B. `fair pull` still needs internet access to download anything listed under `register:` from its original source.

## The `--local` flag

The following commands support the local flag:

- `fair init`
- `fair pull`
- `fair run`
- `fair find`
- `fair identify`

## A local workflow example

With the [FAIR CLI]({{< relref "/docs/cli/installation" >}}) and a [modelling API]({{< relref "/docs/api" >}}) installed, the following runs a model without a remote registry:

```
fair init --local
fair pull --local config.yaml
fair run --local config.yaml
```

A remote registry can be added later by running `fair remote add`, giving a file containing your API token for that registry.

For example:
```
fair remote add https://data.fairdatapipeline.org/api/ --token /path/to/token
```
