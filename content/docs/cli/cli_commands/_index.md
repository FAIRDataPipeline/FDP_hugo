---
weight: 2
title: "FAIR CLI Commands"
bookCollapseSection: false
---

# `fair` CLI Commands

A typical workflow from the command line:

```bash
fair init
fair pull config.yaml
fair run config.yaml
fair status
fair add <namespace>:<data_product>@v<version>
fair push
```

Every command accepts `--debug` for verbose output, and `fair <command> --help` lists a command's options.

## Commands at a glance

| Command | Purpose |
| --- | --- |
| [`fair init`](#fair-init) | Initialise a FAIR project in a git repository |
| [`fair pull`](#fair-pull) | Register and download the inputs listed in a config file |
| [`fair run`](#fair-run) | Run a model and record the code run in the local registry |
| [`fair status`](#fair-status) | Show staged and unstaged code runs and data products |
| [`fair add`](#fair-add) / `fair unstage` / `fair reset` | Stage, unstage, or unstage everything |
| [`fair push`](#fair-push) | Push staged items to a remote registry |
| [`fair list`](#fair-list) | List data products and code runs |
| `fair find` / `fair identify` | Show where a data product is stored / show the details of a file |
| [`fair log`](#fair-log-and-fair-view) / `fair view` | List past jobs / show a job's output |
| [`fair registry`](#fair-registry) | Install, start, stop and inspect the local registry |
| [`fair remote`](#fair-remote) | List, add, modify or remove remote registries |
| `fair create` | Write a starter user *config.yaml* |
| `fair config` | Set the user name (`user.name`) or email (`user.email`) |
| [`fair purge`](#fair-purge) | Remove a project's FAIR setup, or all FAIR files |

## `fair init`

Initialises a FAIR project in the current directory, which should be the root of a git repository. It asks a series of questions to record the user's details (identifier, such as an ORCID or GitHub account, and email), the remote registry, and the data store location, and configures the CLI. It also installs the local registry if it is not installed.

| Option | Description |
| --- | --- |
| `--local` | Initialise without a remote registry (see [Local commands]({{< relref "/docs/cli/cli_commands/local" >}})) |
| `--config <file>` | Also create a starter user *config.yaml* |
| `--export <file>` | Save the current CLI configuration to a file |
| `--using <file>` | Initialise from a saved CLI configuration file |
| `--registry <dir>` | Use a local registry installed in this directory |
| `--ci` | Non-interactive set up for CI systems, using temporary directories |

In a project that is already initialised, `fair init` asks nothing and leaves the project as it is, but registers the project's user in the local registry (useful after the registry has been reinstalled).

## `fair pull`

```bash
fair pull config.yaml
```

- registers anything listed under `register:` in the local registry and downloads it to the local data store
- pulls any data products listed under `read:` that are available on the remote registry, along with their metadata
- pulls metadata for previous versions of the objects listed in `write:`

Use `--local` to pull without a remote registry.

## `fair run`

```bash
fair run config.yaml
```

If no config file is given, the project's default *config.yaml* is used. A shell command can be run directly with `--script`, which writes it into the working config:

```bash
fair run --script 'echo "Hello World"'
```

| Option | Description |
| --- | --- |
| `--dirty` | Allow running with uncommitted changes, or a commit the git remote does not have |
| `--local` | Run without a remote registry |
| `--rehearse` | Prepare the job (working config and script) without running it, printing its files as JSON |
| `--ci` | Prepare and stage the job without running it, for CI systems |

By default `fair run` refuses to run if the repository has uncommitted changes or is behind its git remote, so that every run can be traced to a commit.

### What `fair run` does

- reads (and validates) the user-written *config.yaml* file and checks that everything under `register:` has been pulled
- generates a working *config.yaml* file (see [Working example]({{% ref "/docs/cli/cli_commands/examples/example1" %}}))
  - globbing is used to interpret `*` as all matching objects as well as the original string returned, *e.g.* if `real/data/1` version 0.0.1 and `real/data/thing/1` version 0.0.1 already exist in the registry, the user-written config:

    ```yaml
    write:
    - data_product: real/data/*
      description: general description for all data products
      file_type: csv
      use:
        namespace: someone
        version: ${{MINOR}}
    ```

    is expanded to one entry for each match, each with its own `use:` section, *e.g.*:

    ```yaml
    write:
    - data_product: real/data/1
      description: general description for all data products
      file_type: csv
      use:
        data_product: real/data/1
        namespace: someone
        version: 0.1.0
    ```

    and the pattern's own entry is kept beside them, so that an API can tell a pattern that matched nothing from one never given.
  - each `*` matches one segment of a name: `real/data/*` matches `real/data/1` but not `real/data/thing/1`, which needs `real/data/*/*`

  - specific version numbers and any [template variables](#template-variables) in `run_metadata:`, `register:`, `read:`, and `write:` are replaced with true values
  - a version of `${{MAJOR}}`, `${{MINOR}}` or `${{PATCH}}` increments that part of the latest version, and `${{LATEST}}` uses the latest version
  - if no version is given, a `write:` entry increments the patch number if the data product already exists (otherwise the version is 0.0.1), and a `read:` entry uses the latest version
  - `register:` is removed and `external_object`s are written to `read:` as `data_product`s
  - the `public:` field in `run_metadata:` is populated (default is `true`)
  - the `version:` field in each `use:` section is populated
- records the latest commit of the git repository in `run_metadata: latest_commit:` and its remote URL in `run_metadata: remote_repo:`
- saves the working *config.yaml* and the submission script in the local data store, in *<data_store>/coderun/\<date>T\<time>/*
  - the config should contain either `script:`, the commands to run, or `script_path:`, the path of a script to run
- sets the environment variables `FDP_CONFIG_DIR` (the folder above), `FDP_LOCAL_TOKEN` (the local registry token) and `FDP_SESSION` for the script
- executes the submission script

### Choosing a shell

By default the script is run with `sh` on Unix and `batch` on Windows. Set `shell:` in `run_metadata:` to use another, where `{0}` is the script file:

| **Shell**    | **Command**                     |
| ------------ | ------------------------------- |
| `bash`       | `bash -eo pipefail {0}`         |
| `java`       | `java {0}`                      |
| `julia`      | `julia {0}`                     |
| `powershell` | `powershell -command ". '{0}'"` |
| `pwsh`       | `pwsh -command ". '{0}'"`       |
| `python2`    | `python2 {0}`                   |
| `python3`    | `python3 {0}`                   |
| `python`     | `python {0}`                    |
| `R`          | `R -f {0}`                      |
| `sh`         | `sh -e {0}`                     |
| `batch`      | `{0}`                           |

Flags may follow the shell's name: `shell: julia --project=@.` runs `julia --project=@. {0}`.

### Template variables

The following variables can be used in a *config.yaml* file with the notation `${{ VAR }}`:

| **Variable**        | **Description**                                                         |
| ------------------- | ----------------------------------------------------------------------- |
| `DATE`              | Date in the form `%Y%m%d`                                               |
| `DATETIME`          | Date and time in the form `%Y-%m-%dT%H:%M:%S`                           |
| `DATETIME-%Y%H%M`   | Date and time in a custom format (where `%Y%H%M` can be any valid form) |
| `USER`              | The current user as defined in the CLI                                  |
| `USER_ID`           | The unique identifier for the current user                              |
| `REPO_DIR`          | The FAIR repository root directory                                      |
| `CONFIG_DIR`        | The directory containing the working *config.yaml*                      |
| `LOCAL_TOKEN`       | The token for access to the local registry                              |
| `SOURCE_CONFIG`     | Path of the user-written *config.yaml*                                  |
| `GIT_BRANCH`        | Current branch of the git repository                                    |
| `GIT_REMOTE`        | The URI of the git repository specified during setup                    |
| `GIT_TAG`           | The latest tag on git                                                   |

## `fair status`

Shows code runs and data products awaiting staging (red) and staged (green), like `git status`.

## `fair add`

Stages a code run or data product to be pushed to the remote registry.

- a code run is given by its UUID, *e.g.* `5062cc4f-2c45-48f5-989a-6fe7fe0452ca`
- a data product is given as `<namespace>:<data_product name>@v<version>`, *e.g.* `PSU:SEIRS_model/parameters@v1.0.0`

`fair unstage <identifier>` removes an item from staging, and `fair reset` unstages everything.

## `fair push`

```bash
fair push [remote]
```

Pushes staged items to the remote registry (`origin` by default): metadata is recorded in the remote registry and new files are uploaded to its data store.

## `fair list`

Lists data products and code runs in the local registry. Use `fair list data-products` or `fair list code-runs` for one of them, and `--remote <name>` to list those on a remote registry.

## `fair log` and `fair view`

`fair log` lists the jobs run in the project, like `git log`. `fair view <sha>` shows the output of a job, given the first few characters of its SHA. A job's SHA is not the same as a code run's UUID, as one job can record several code runs.

## `fair registry`

Commands that use the local registry (`run`, `pull`, `push`, `status`, `list`, `find`, `identify`, `reset`) start it if it is not running, and stop it again when the last of them finishes.

| Command | Description |
| --- | --- |
| `fair registry install` | Install the local registry (`--directory <dir>`, `--version <tag or branch>`, `--force` to replace an existing one) |
| `fair registry uninstall` | Remove the local registry |
| `fair registry start` | Start the registry and keep it running (`--port`, `--address`), *e.g.* to browse it at http://localhost:8000 |
| `fair registry stop` | Stop the registry (`--force` to stop it while a command is using it) |
| `fair registry status` | Show where the registry is running and which processes are using it |
| `fair registry session open --pid <pid> --json` / `close <id>` | Hold the registry open for a program outside `fair`, such as a language API |

## `fair remote`

`fair remote` lists the configured remote registries.

```bash
fair remote add [label] <url> --token <token file>
fair remote modify <label> <url>
fair remote remove <label>
```

The label defaults to `origin`.

## `fair purge`

Removes the FAIR setup of the current project so it can be reinitialised.

| Option | Description |
| --- | --- |
| `--global` | Also remove the global CLI configuration |
| `--data` | Also remove the data directory (not recommended, as the registry may still point to it) |
| `--all` | Remove the project's `.fair` folder and all of `~/.fair`, including the local registry |
| `--yes` | Skip confirmation prompts |
