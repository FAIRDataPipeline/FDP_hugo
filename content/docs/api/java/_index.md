---
weight: 4
title: "Java"
bookCollapseSection: true
---

# javaDataPipeline

The javaDataPipeline Java library allows Java modellers to interface with the FAIR Data Pipeline. The current release, 1.0.0-rc3, requires Java 11 or later and is available on [Maven Central](https://repo1.maven.org/maven2/org/fairdatapipeline/api/):

```gradle
dependencies {
    implementation 'org.fairdatapipeline:api:1.0.0-rc3'
}
```

The [code repo][repo] is at 1.0.0-rc4, which has the same API but requires Java 21 or later. It is not yet on Maven Central, so to use it, build it from the repo.

The main purpose of interfacing with the FAIR Data Pipeline is the recording of Coderuns: a Coderun is a recorded session of your modelling code, all the inputs used and outputs generated will be recorded and a [Provenance Report][prov] will be generated.

The basic idea behind the FAIR Data Pipeline and Coderuns is explained on the [FAIR CLI]({{< relref "/docs/cli" >}}) page.

## What does it implement?

* It reads the working *config.yaml* written by `fair run` (not to be confused with the user-written *config.yaml*).
* It registers a model Coderun session in the [FAIR Data Registry]({{< relref "/docs/data_registry" >}}), including all the data inputs and outputs of the model, its code repository, its *config.yaml*, and its submission (startup) script.
* It allows the Coderun session to raise Issues with Object_components, the code repository, the config file and the submission script, and these Issues are registered in the Data Registry.
* It allows reading and writing of Data_products holding a whole file in any ('external') file format.
* It allows reading and writing of Data_products containing a FAIR Data Pipeline (TOML) [parameter file](parameters).
* netCDF files, recommended for structured data, are read and written whole, like any other file (see [netCDF](netcdf)).

## Usage

A useful starting point to get to know the javaDataPipeline is to explore the [java Simple Model](https://github.com/FAIRDataPipeline/javaSimpleModel). This shows example *config.yaml* files (see also the [CLI example]({{< relref "/docs/cli/cli_commands/examples/example1" >}})), example
`main(String[] args)` code that allows `fair run` to call your code, and example `Coderun()` code for reading/writing input/output Data Products.

The links below give a basic introduction to the javaDataPipeline; these should be read in conjunction with the [javaDocs][docs] which give more precise specification of the java API.

[Basic Coderun](coderun)  
[Issues](issues)   
[Parameters](parameters)  
[netCDF](netcdf)   
[Run](run)  
[Debug](debug)  

See the [package documentation][docs] for Javadoc.


## License/copyright

Copyright (c) 2021: Bram Boskamp, Biomathematics and Statistics Scotland and the Scottish COVID-19 Response Consortium

GNU LESSER GENERAL PUBLIC LICENSE Version 3


## Source code

See the package's [code repo][repo].

[docs]: https://www.fairdatapipeline.org/javaDataPipeline
[repo]: https://github.com/FAIRDataPipeline/javaDataPipeline
[prov]: /docs/data_registry/prov_report/
