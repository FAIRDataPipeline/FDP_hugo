---
weight: 40
---

# netCDF

[netCDF](https://www.unidata.ucar.edu/software/netcdf/) is the recommended format for structured data such as arrays and tables. Like any file that is not a TOML parameter file, a netCDF file is read or written whole, through one unnamed `Object_component`, using a netCDF library such as [NetCDF-Java](https://docs.unidata.ucar.edu/netcdf-java/current/userguide/).

Writing, with the file type `nc`:

```
    try (var coderun = new Coderun(configPath, scriptPath, token)) {
      Data_product_write dp = coderun.get_dp_for_write(dataProduct, "nc");
      Object_component_write oc = dp.getComponent();
      Path p = oc.writeLink();
      write_my_netcdf_data_to_path(p);
    }
```

And reading:

```
    try (var coderun = new Coderun(configPath, scriptPath, token)) {
      Data_product_read dp = coderun.get_dp_for_read(dataProduct);
      Object_component_read oc = dp.getComponent();
      Path p = oc.readLink();
      read_my_netcdf_data_from_path(p);
    }
```
