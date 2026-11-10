---
title: "RO Crate Objects"
weight: 5
---

# Research Object Crate (RO-Crate)

A [Research Object Crate](https://www.researchobject.org/ro-crate/) (RO-Crate) packages research data together with the metadata that describes it, in a single folder or zip file with a machine-readable description, `ro-crate-metadata.json`. The data registry can produce an RO-Crate for any data product or code run, so that results can be shared or archived together with their provenance: the code that made them, the inputs it read and where those came from.

The crates conform to the [Process Run Crate 0.6](https://w3id.org/ro/wfrun/process/0.6) profile of the [Workflow Run RO-Crate](https://www.researchobject.org/workflow-run-crate/) family, which describes how a piece of software was run to produce files.

## Getting a crate

Crates are available through the REST API:

| Address | Crate for |
| --- | --- |
| `api/ro-crate/data-product/<id>/` | a data product, and the files that were used to produce it |
| `api/ro-crate/code-run/<id>/` | a code run: all of its outputs, and the files it used |
| `api/ro-crate/?data_product=<id>&data_product=<id>` | several data products, *e.g.* outputs of different code runs, together with the provenance of each |

The API records of each data product and code run link to their crates in their `ro_crate` field.

### Formats

The format is chosen with the `format` query parameter or the request's `Accept` header:

| `format` | Description |
| --- | --- |
| `zip` | the crate as a zip file, holding `ro-crate-metadata.json` and the files chosen by `pack` |
| `json` or `json-ld` | the contents of `ro-crate-metadata.json` alone, with no files |
| `api` | the metadata in the registry's browsable API page (default in a browser) |

For example, to download the crate of data product 3 with two levels of provenance:

```
https://data.fairdatapipeline.org/api/ro-crate/data-product/3/?format=zip&depth=2
```

### Query parameters

| Parameter | Description |
| --- | --- |
| `depth` | how many levels of code runs to include (default `1`): each further level adds the code runs that produced the previous level's inputs, and a data product reached by several routes is added once |
| `level` | what is recorded about every file: `1`, its hash and any persistent identifier, with its source's metadata; `2`, also the address the registry's copy can be downloaded from (`contentUrl`). The default is `2` for a zip and `1` otherwise |
| `pack` | which files' contents a zip holds (see below); the default is `roots` |

### Choosing which files are packed

Every file in the crate's provenance is described in the metadata, but only some are copied into a zip:

| `pack` | Files packed |
| --- | --- |
| `none` | none |
| `roots` | the data products the crate is for (for a code run's crate, its outputs) |
| `supplements` | as `roots`, plus every file registered from a supplementary source: data that was requested or derived from a source, such as an extract made by a data service, which no identifier would return again |
| `all` | every file |

The working config and submission script of each code run are always packed in a zip. Only public files are packed: a file that is not public is described by the address of its storage location in the registry. Outputs of a code run that are outside the crate's provenance are described, without `contentUrl`, and never packed.

Files from primary sources, which can be fetched again by their identifier (such as a DOI), only need to be packed with `all`. A crate packed with `roots` therefore stays small while still recording where every input can be found.

## Contents of a crate

### Layout

Each data product's file is at `<namespace>/<name>/<version>.<extension>`, the same layout a registry's data store uses for the files it pulls, and each code run's working config and submission script are in `model_config/` and `submission_script/` under their own file names. Every file has this path in the metadata whether or not it is packed. For example, the crate of the results of the Python SEIRS model (written to the namespace `user_4`) holds:

```
ro-crate-metadata.json
PSU/
  SEIRS_model/
    parameters/
      1.0.0.csv
user_4/
  SEIRS_model/
    results/
      figure/
        python/
          0.0.1.png
      model_output/
        python/
          0.0.1.csv
model_config/
  config.yaml
submission_script/
  script.sh
```

### Files

Each file is a `File` entity with:

- `name` and `version`: the data product's name and version
- `namespace`: a `Namespace` entity, with the namespace's `name`, its full name as `alternateName`, and its website as `url`
- `identifier`: the `uuid` of the file's object in the registry
- `sha1`: the SHA-1 hash of the file's contents
- `encodingFormat`: the file's media type, from its file type
- `description`, and `contentUrl` at `level=2`
- `author`: the file's authors
- `issue`: any issues raised against the file, each as `<uuid> severity <severity>: <description>` (`issue` is one of the registry's own terms, defined on its [vocabulary page](https://data.fairdatapipeline.org/vocab/))

The working config and submission script of each code run are entities of type `File` and `SoftwareSourceCode`, with their file name as `name` and the same `identifier`, `sha1`, `encodingFormat`, `description` and `author` properties.

### External sources

A data product registered from outside the pipeline (an `external_object:` in the user config) is a `File` like any other, and its source is a `Dataset` entity: its persistent identifier (such as a DOI) as `identifier`, its title as `name`, and its `version`, `datePublished`, `description`, alternate identifier and, where the registry recorded where the file was fetched from, `contentUrl`. There is one source entity per source the registry knows.

- A file from a **primary source**, where the file is the identified item or one of its files, points to the source with `sameAs`.
- A file from a **supplementary source**, where the data was requested or derived from the source, points to it with `isBasedOn`. The source's `description` holds the request, where the registry was given it, so the file can be reproduced.
- A deposit registered as a whole is a source of its own, and the sources of files under its identifier are `isPartOf` it.

### Code runs

Each code run is a `CreateAction`, as described in [Software used to create files](https://www.researchobject.org/ro-crate/1.1/provenance.html#software-used-to-create-files), with:

| Property | Value |
| --- | --- |
| `identifier` | the code run's `uuid`, which is also in its `@id` (`urn:uuid:<uuid>`) |
| `name`, `description`, `startTime` | `code run <uuid>`, the code run's description, and when it was run |
| `instrument` | the code that was run (or the submission script, for a run without a code repository) |
| `object` | the input files, the working config and the submission script |
| `model_configuration`, `submission_script` | which of those files is the working config and which the submission script |
| `result` | every output of the code run |
| `agent` | the author who ran the code |

The `instrument` is a `SoftwareApplication` identified by the repository's address and the commit that was run, as `<repository>#<commit>`, with the repository as its `url`. For a code release (a `CodeRepoRelease`), its `name` and `softwareVersion` are the release's name and version; otherwise its `name` is the repository's path and its `version` is the commit.

### People and licences

- Authors are identified by their ORCID, GitHub account or other identifier where the registry has one, and otherwise by their `uuid` (as `urn:uuid:<uuid>`). An author with a ROR id is an `Organization`, and any other a `Person`. A user with no linked author is identified by a local id.
- The licences of the files the crate is for are the crate's `license`, identified by their URL, or by the file they apply to where they have none. A crate whose files have no licence is licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Individual files may carry their own licences.
- `ro-crate-metadata.json` itself is made available under the [CC0 Public Domain Dedication](https://creativecommons.org/publicdomain/zero/1.0/).
- The crate's `publisher` is `FAIR Data Pipeline`, and its `datePublished` is when it was generated.

Nothing in a crate is identified by its row number in the registry that produced it.

## Example

This is part of the `ro-crate-metadata.json` of the crate above (`api/ro-crate/data-product/7/?format=json-ld`), produced by a local registry loaded with the registry's example data (`python manage.py add_example_data`). It shows the code run that wrote the results, the code it ran, and the parameters it read, which were registered from a primary source. The root dataset, licences, people and other files, and the files' `encodingFormat`, are left out:

```json
{
  "@context": [
    "https://w3id.org/ro/crate/1.2/context",
    {
      "sha1": "https://w3id.org/ro/terms/workflow-run#sha1",
      "Namespace": "https://data.fairdatapipeline.org/vocab/#Namespace",
      "namespace": "https://data.fairdatapipeline.org/vocab/#namespace",
      "model_configuration": "https://data.fairdatapipeline.org/vocab/#model_configuration",
      "submission_script": "https://data.fairdatapipeline.org/vocab/#submission_script",
      "alternate_identifier": "https://data.fairdatapipeline.org/vocab/#alternate_identifier",
      "alternate_identifier_type": "https://data.fairdatapipeline.org/vocab/#alternate_identifier_type"
    }
  ],
  "@graph": [
    {
      "@id": "urn:uuid:b79b6dcf-ff35-45cc-9bb8-2377cc0d1735",
      "@type": "CreateAction",
      "name": "code run b79b6dcf-ff35-45cc-9bb8-2377cc0d1735",
      "identifier": "b79b6dcf-ff35-45cc-9bb8-2377cc0d1735",
      "startTime": "2021-10-07T12:15:54.842457+00:00",
      "description": "SEIRS Model python",
      "agent": {
        "@id": "urn:uuid:f0d16259-1830-4c6e-bd87-08d7880298bf"
      },
      "model_configuration": {
        "@id": "model_config/config.yaml"
      },
      "submission_script": {
        "@id": "submission_script/script.sh"
      },
      "instrument": {
        "@id": "https://github.com/FAIRDataPipeline/pythonFDP.git#5990e80e3e6f0cd6d81e702926518ae16fa18e88"
      },
      "object": [
        {
          "@id": "model_config/config.yaml"
        },
        {
          "@id": "submission_script/script.sh"
        },
        {
          "@id": "PSU/SEIRS_model/parameters/1.0.0.csv"
        }
      ],
      "result": [
        {
          "@id": "user_4/SEIRS_model/results/model_output/python/0.0.1.csv"
        },
        {
          "@id": "user_4/SEIRS_model/results/figure/python/0.0.1.png"
        }
      ]
    },
    {
      "@id": "PSU/SEIRS_model/parameters/1.0.0.csv",
      "@type": "File",
      "name": "SEIRS_model/parameters",
      "version": "1.0.0",
      "identifier": "40412500-9996-45bb-9e7c-28615445c5e9",
      "sha1": "6294a5951677e6b8438cabf55234b7974adeaee3",
      "description": "Static parameters of the model",
      "namespace": {
        "@id": "#namespace-PSU"
      },
      "sameAs": {
        "@id": "#source-https%3A%2F%2Fdoi.org%2F10.1038%2Fs41592-020-0856-2%3AStatic%20parameters%20of%20the%20model%401.0.0"
      },
      "author": [
        {
          "@id": "urn:uuid:c158b2b1-ebfb-4d83-acf3-0597ffcae0bc"
        }
      ]
    },
    {
      "@id": "#source-https%3A%2F%2Fdoi.org%2F10.1038%2Fs41592-020-0856-2%3AStatic%20parameters%20of%20the%20model%401.0.0",
      "@type": "Dataset",
      "name": "Static parameters of the model",
      "version": "1.0.0",
      "datePublished": "2021-09-20T12:00:00+00:00",
      "identifier": "https://doi.org/10.1038/s41592-020-0856-2",
      "alternate_identifier": "SEIRS model parameters - Static parameters of the model",
      "alternate_identifier_type": "SEIRS_model_params"
    },
    {
      "@id": "#namespace-PSU",
      "@type": "Namespace",
      "name": "PSU",
      "alternateName": "Pennsylvania State University",
      "url": "https://ror.org/04p491231"
    },
    {
      "@id": "https://github.com/FAIRDataPipeline/pythonFDP.git#5990e80e3e6f0cd6d81e702926518ae16fa18e88",
      "@type": "SoftwareApplication",
      "url": "https://github.com/FAIRDataPipeline/pythonFDP.git",
      "name": "pythonFDP",
      "softwareVersion": "1.0.0",
      "author": [
        {
          "@id": "urn:uuid:c158b2b1-ebfb-4d83-acf3-0597ffcae0bc"
        }
      ]
    }
  ]
}
```
