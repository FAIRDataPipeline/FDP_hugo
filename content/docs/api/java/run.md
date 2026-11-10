---
weight: 50
---

# Running your code

Below is a brief explanation how to make use of the Java FAIRDataPipeline library and actually getting your code to run from the [FAIR CLI][faircli] command line interface. The actual command to run your Java code is given in the `script:` line in the *config.yaml*. In my case I run the Java code using `gradle run --args "${{CONFIG_DIR}}"`. You'll have to change this if you don't use gradle.

Below is an example *config.yaml*, stored in your projects /src/main/resources:

```
run_metadata:
  default_input_namespace: test
  description: Java test coderun
  script: |
    gradle run --args "${{CONFIG_DIR}}"

write:
- data_product: test/results/my-result
  description: test results
  file_type: csv
```

Your Java code then needs to read the registry token from the environment variable, and create a coderun using the *config.yaml* and *script.sh* files that will be placed in the CONFIG_DIR by [FAIR CLI][faircli]:

```
String token = System.getenv("FDP_LOCAL_TOKEN");
Path configPath = Path.of(args[0]).resolve("config.yaml");
Path scriptPath = Path.of(args[0]).resolve("script.sh");
try(var coderun = new Coderun(configPath, scriptPath, token)) {
	// create DP, Object_component, write results;
}
```

From your project root you can then just run the [FAIR CLI][faircli] commands: 

```
fair init
fair pull src/main/resources/config.yaml
fair run src/main/resources/config.yaml
```




[faircli]: /docs/cli/
