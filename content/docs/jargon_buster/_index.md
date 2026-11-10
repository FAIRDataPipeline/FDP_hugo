---
weight: 5
title: "Jargon buster"
bookCollapseSection: false
---

# Jargon buster

## C

`config.yaml`
: a file that specifies the metadata used during file lookup (for read, write, and register), used to configure the overall API behaviour.

Code run
: a recorded run of a model or script, along with its code repository and commit, config file, submission script, inputs and outputs

## D

Data product
: a file recorded in the pipeline, identified by its namespace, name and version; any file format can be a data product, and the parameters in TOML parameter files can also be read and written individually

Data registry
: a database for recording metadata about data and model outputs; see local registry and remote registry

Data store
: the folder in which the files registered in a registry are kept (by default `~/.fair/data` for the local registry)

## E

Environment variables (`FDP_CONFIG_DIR`, `FDP_LOCAL_TOKEN`)
: set by `fair run` for the submission script: the folder holding the working `config.yaml` and submission script, and the local registry's API token

External object
: data from outside the pipeline, such as a published dataset identified by a DOI, registered with `register:` in the user-written `config.yaml`; the data products registered from it record it as their source

## F

`finalise()`
: calculates the hash of the external objects / data products and renames files, check files exist, etc.

## I

`initialise()`
: reads in the `config.yaml` file, generates a new entry in `code_run` and returns id, creates new `submission_script` (and if necessary creates a new `code_repo`) entry in local registry

## L

Local registry
: a data registry running on your own machine, installed and managed by the CLI (`fair registry install`)

`link_read()`
: connects `code_run` to `external_object` as input and returns path location (see also `link_write()`)

`link_write()`
: connects `code_run` to `data_product` as output and returns path location (see also `link_read()`)

## N

Namespace
: a way of grouping data products, usually by the person or organisation that produced them

## R

Remote registry
: a shared data registry, such as https://data.fairdatapipeline.org, with which a local registry is synchronised using `fair pull` and `fair push`

`read:`
: a field in `config.yaml` used to specify the reading of an object (a data product or an external object) that belongs to the user (see also `write:` and `register:`)

`register:`
: a field in `config.yaml` used to specify the registration of an object (usually an external object) that exists elsewhere (see also `write:` and `read:`)

`add_to_register()`
: registers the object(s) listed in `register:` (in the `config.yaml` file)

## W

Working config
: the `config.yaml` written by `fair run` from the user-written one, with variables, wildcards and versions resolved; this is the file the language APIs read

`write:`
: a field in `config.yaml` used to specify the writing of an object (a data product or an external object) that belongs to the user (see also `register:` and `read:`)