---
weight: 10
---

# javaDataPipeline Coderun

The main class used to interface with the FAIR DataPipeline in Java, is the `Coderun` class.

Users should initialise the `Coderun` instance using a *try-with-resources* block or ensure that `.close()` is explicitly called upon finishing.

The `Coderun` constructor needs a Path to the config.yaml, a Path to the script.sh, and the registry authentication token.

The user then would access the input data product(s) using `coderun.get_dp_for_read(dataproduct_name)`
and output data product(s) using `coderun.get_dp_for_write(dataproduct_name, extension)`.

From the resulting data_product we can then access either one unnamed object_component: `Object_component_read oc = dp.getComponent()`
or any number of named object_components: `Object_component_read oc1 = dp.getComponent('ComponentName')`.


Data in the FAIR Data Pipeline's internal format (TOML parameter files) is written to (or read from) named `Object_components`, while other file formats (such as CSV or netCDF) are written to (or read from) just 1 unnamed `Object_component`.

Here is an example of a `Coderun` writing a CSV file to one unnamed `Object_component`:

```
    try (var coderun = new Coderun(configPath, scriptPath, token)) {
      Data_product_write dp = coderun.get_dp_for_write(dataProduct, "csv");
      Object_component_write oc = dp.getComponent();
      Path p = oc.writeLink();
      write_my_csv_data_to_path(p);
    }
```

And for reading:

```
    try (var coderun = new Coderun(configPath, scriptPath, token)) {
      Data_product_read dp = coderun.get_dp_for_read(dataProduct);
      Object_component_read oc = dp.getComponent();
      Path p = oc.readLink();
      read_my_csv_data_from_path(p);
    }
```


Here is an example of a coderun writing Samples to one named Object_component:

```Java
    try (var coderun = new Coderun(configPath, scriptPath)) {
       ImmutableSamples samples = ImmutableSamples.builder().addSamples(1, 2, 3).rng(rng).build();
       String dataProduct = "animal/dodo";
       String component1 = "example-samples-dodo1";
       Data_product_write dp = coderun.get_dp_for_write(dataProduct, "toml");
       Object_component_write oc1 = dp.getComponent(component1);
       oc1.writeSamples(samples);
    }
```

