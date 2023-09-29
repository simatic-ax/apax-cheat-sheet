# Apax Cheat Sheet

## 01 Apax initial work

### Apax

|                                           |                                                                                                                        |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `apax self-update`                        | Update Apax itself.                                                                                                    |
| `apax --version`                          | returns the version of the installed apax                                                                              |
| `apax remove-versions --version=versions` | A space separated list of versions, to remove. If not provided all versions other then the current one will be removed |

### Login

|                                                                |                                                             |
| -------------------------------------------------------------- | ----------------------------------------------------------- |
| `apax login`                                                   | interactive login                                           |
| `apax login --registry REGISTRY-URL [--password ACCESS-TOKEN]` | Login to a custom registry e.g. https://npm.pkg.github.com/ |

> a login might be required to install dependencies from registries, where a authentication is required

### Licensing

|                       |                                           |
| --------------------- | ----------------------------------------- |
| `apax update-license` | Update local license file for Apax tools. |

## 02 Setup workspaces

| command                                                    |                                                                          |
| ---------------------------------------------------------- | ------------------------------------------------------------------------ |
| `apax create`                                              | create a new project interactively                                       |
| `apax create TEMPLATE NAME`                                |                                                                          |
| `apax create TEMPLATE --registry=custom-registry-url NAME` | Create a workspace from a template, which is not on the default registry |

### Options

| command                    | short |                                                                                             |
| -------------------------- | ----- | ------------------------------------------------------------------------------------------- |
| `apax create --postcreate` | `-p`  | Run an existing `postcreate` script during creation.                                        |
| `apax create --here`       |       | Create the new project directly in the current directory, instead of using a new directory. |
| `apax create --no-git`     |       | Skip the creation and initialization of a git repository.                                   |

### Example for `postcreate` script

Command: `apax create my-template --registry=https://npm.pkg.github.com/ --postcreate`

This command execute the `postcreate` script, when it is defined in the template `my-template`. In this example, the dependencies will be installed automatically.

```yml
scripts:
  postcreate: apax install
```

## 03 Install dependencies

| command                      | short |                                                                                                                                                                                                                                                                                                                  |
| ---------------------------- | ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `apax install`               |       | Installs dependencies based on apax.yml manifest and creates a apax-lock.json file. If a lock file exists dependency information will be used for installation except when apax.yml defines a specific version to be used. For details see below                                                                 |
| `apax install --immutable`   | `-i`  | Performs a clean & reproduceable install by clearing before installing dependencies from scratch. Lockfile will be used as source for dependency metadata. If apax.yml and Lockfile are out of sync (apax.yml defines different version than apax-lock.json) the installation will fail with non-zero exit code. |
| `apax install  --copy-local` | `-c`  | Copy packages instead of linking to the global cache.                                                                                                                                                                                                                                                            |
| `apax install  --redownload` | `-r`  | Redownload packages, even if they are already available in the global cache.                                                                                                                                                                                                                                     |

Dependencies will be installed according the semantic versioning

| apax.yml | apax-lock.json | latest version on registry | apax install option | installed version                      |
| -------- | -------------- | -------------------------- | ------------------- | -------------------------------------- |
| 2.0.1    | -              | 2.1.2                      | -                   | 2.0.1                                  |
| ^2.0.1   | -              | 2.1.2                      | -                   | 2.1.1                                  |
| ~2.0.1   | -              | 2.1.2                      | -                   | 2.0.2                                  |
| ^2.0.1   | 2.0.1          | 2.1.2                      | -                   | 2.0.1                                  |
| ^2.0.1   | 2.0.1          | 2.1.2                      | --immutable         | 2.0.1                                  |
| 2.0.2    | 2.0.1          | 2.1.2                      | -                   | 2.0.2                                  |
| 2.0.2    | 2.0.1          | 2.1.2                      | --immutable         | Error, manifest & lockfile in conflict |

### Options

| command                         |                                                |
| ------------------------------- | ---------------------------------------------- |
| `apax install --ignore-scripts` | Do not run preinstall and postinstall scripts. |

### Examples for `apax install`

```yml
devDependencies:
  "@ax/sdk": X.Y.Z #latest version 4.0.8
```

| `@ax/sdk": X.Y.Z`  | installed version |
| ------------------ | ----------------- |
| `@ax/sdk": 4.0.2`  | 4.0.2             |
| `@ax/sdk": ^4.0.2` | 4.0.8             |
| `@ax/sdk": 3.0.2`  | 3.0.2             |
| `@ax/sdk": ^3.0.2` | 3.0.19            |

## 04 Update dependencies

### Commands

| command                     | short |                                                                           |
| --------------------------- | ----- | ------------------------------------------------------------------------- |
| `apax update`               |       | Interactive update of all dependencies according the semantic versioning. |
| `apax update PACKAGE`       |       | The `PACKAGE` that should be updated (non-interactive)                    |
| `apax update --all`         | `-a`  | Update all package (non interactive) according the semantic versioning    |
| `apax update --forceLatest` | `-f`  | Force latest version.                                                     |

### Options

### Example for `apax update`

```yml
devDependencies:
  "@ax/sdk": X.Y.Z  // latest version 4.0.8
```

| `@ax/sdk": X.Y.Z`  | result                                            |
| ------------------ | ------------------------------------------------- |
| `@ax/sdk": 4.0.2`  | No updates available                              |
| `@ax/sdk": ^4.0.2` | @ax/sdk 4.0.2 -> 4.0.8                            |
| `@ax/sdk": 3.0.2`  | No updates available                              |
| `@ax/sdk": ^3.0.2` | @ax/sdk 3.0.2 -> 3.0.19 (major version is pinned) |

### Example for `apax update --forceLatest`

```yml
devDependencies:
  "@ax/sdk": x.y.z  // latest version 4.0.8
```

| entry in apax.yml  | result                 |
| ------------------ | ---------------------- |
| `@ax/sdk": 4.0.2`  | @ax/sdk 4.0.2 -> 4.0.8 |
| `@ax/sdk": ^4.0.2` | @ax/sdk 4.0.2 -> 4.0.8 |
| `@ax/sdk": 3.0.2`  | @ax/sdk 3.0.2 -> 4.0.8 |
| `@ax/sdk": ^3.0.2` | @ax/sdk 3.0.2 -> 4.0.8 |

### Example for `apax update <PACKAGE>`

```yml
devDependencies:
  "@ax/sdk": 4.0.2 #latest version 4.0.8
dependencies:
  "@ax/system-timer": 3.0.1 #latest 4.0.1
```

The command `apax update @ax/system-timer` will just update the package `@ax/system-timer` from 3.0.1 to version 4.0.1

## 05 Add/Remove dependencies

### Commands

| command                  | short |                                                                     |
| ------------------------ | ----- | ------------------------------------------------------------------- |
| `apax add PACKAGE`       |       | The name of the package to add. It calls implicit an `apax install` |
| `apax add PACKAGE --Dev` | `-D`  | Whether to add the package as a development dependency.             |
| `apax remove PACKAGE`    |       | The name of the package to remove.                                  |

### Further options

| command                         |                                                |
| ------------------------------- | ---------------------------------------------- |
| `apax install --ignore-scripts` | Do not run preinstall and postinstall scripts. |

## 06 Build

### Commands

| command      |                                                                                                |
| ------------ | ---------------------------------------------------------------------------------------------- |
| `apax build` | Build the project using the ST compiler. Make sure the @ax/sdk or @ax/st package is installed. |

### Options

| command                            | short |                                                                                  |
| ---------------------------------- | ----- | -------------------------------------------------------------------------------- |
| `apax build --variables=variables` | `-v`  | Add variables during build, corresponding apax.yml variables will be overridden! |
| `apax build  --ignore-scripts`     |       | Do not run prebuild and postbuild scripts.                                       |

### Example for a postbuild script

This `postbuild` script executes the unit tests automatically. when the build was successful.

```yml
scripts:
  postbuild: apax test
```

## 07 Test

### Commands

| command     |                                                                             |
| ----------- | --------------------------------------------------------------------------- |
| `apax test` | Run AxUnit tests. Make sure the @ax/sdk or @ax/axunit package is installed. |

### Options

| command                        | short |                                          |
| ------------------------------ | ----- | ---------------------------------------- |
| `apax build --coverage`        | `-c`  | Specifies to get coverage.               |
| `apax build  --ignore-scripts` |       | Do not run pretest and posttest scripts. |

## 08 Package creation

### Create a package

| command     |                                                                                                                 |
| ----------- | --------------------------------------------------------------------------------------------------------------- |
| `apax pack` | Create a package that can be published. Only files specified in the files section of the apax.yml are included. |

### Options

| command                      | short |                                                                                                             |
| ---------------------------- | ----- | ----------------------------------------------------------------------------------------------------------- |
| `apax pack --key=key`        | `-k`  | The private key to sign the package with, instead of taking it from the default file. Begins with "SECRET". |
| `apax pack --ignore-scripts` |       | Do not run prepack and postpack scripts.                                                                    |

> `apax pack` signs every package. If no key exists, a new key will be generated.

### Example:

```yml
name: myproject
version: 1.0.0
files:
  - README.md
  - src
```

Given the files section in the apax.yml. Apax pack creates a package `myproject-1.0.0.apax.tgz` and includes the README.md and the src folder.

### Publish a package

| command                                              |                                                    |
| ---------------------------------------------------- | -------------------------------------------------- |
| `apax publish --package=package --registry=registry` | Publish a given package to the specified registry. |

### Signing of packages

| command                                 |                                                                                                                                         |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `apax sign --package=package`           | Sign an existing package. Note that the pack command also signs packages and is preferred; this one is provided for advanced use cases. |
| `apax sign --package=package --key=key` | Same as above. But instead of taking it from the default file, you can use another key                                                  |

> signed packages protects of manipulated packages.
> `apax pack` signs every package. If no key exists, a new key will be generated.

### Generate keys

| command                           |                                                                                  |
| --------------------------------- | -------------------------------------------------------------------------------- |
| `apax keygen`                     | Creates a key-file, consisting of private and public key, if no key-file exists. |
| `apax keygen --override-existing` | Override an existing key-file with a new private and public key.                 |

#### Example of a key pair

```
created: 2023-08-30T16:30:00.000Z
publicKey: 1234effe0123abba5678fefe7890abcd1234def4321dcba00001234affebcda1
privateKey:SECRET9239823897cdfd7d7a1aff4e7e8c8d8a88e88d8765a852b2cc4c7af241618936a6c7e7d58a8b7c7726125c613c3f3e33d3e3a3124ac3c1c1c31fa6a8c7b7a7f2
```

> WARNING: never publish the private key!

## 09 Typical workflows

### Create workspace for a PLC application, library or a TIAX workflow via CLI and open it in AxCode

|     |                              |                                                                                      |
| --- | ---------------------------- | ------------------------------------------------------------------------------------ |
| 1.  | _open a command line window_ |
| 2.  | `cd <destination directory>` | select the destination directory eg. `cd \temp\`                                     |
| 3.  | `apax create TEMPLATE NAME`  | TEMPLATE = [**app, lib, tiax**], NAME = **your workspace name** (e.g. `myWorkspace`) |
| 4.  | `cd NAME`                    | change to the directory e.g. `cd myWorkspace`                                        |
| 5.  | `apax install -L`            | install the dependencies                                                             |
| 6.  | `axcode .`                   | open AX Code                                                                         |

### Create workspace for a PLC application, library or a TIAX workflow via CLI in an opened AxCode

|     |                                                      |                                                                                      |
| --- | ---------------------------------------------------- | ------------------------------------------------------------------------------------ |
| 1.  | _AxCode is already open_                             |
| 2.  | _Select `File-->Close folder` if a folder is opened_ |
| 3.  | _Open a terminal in AxCode_                          |
| 4.  | `cd <destination directory>`                         | select the destination directory eg. `cd \temp\`                                     |
| 5.  | `apax create <TEMPLATE> <NAME> --here`               | TEMPLATE = [**app, lib, tiax**], NAME = **your workspace name** (e.g. `myWorkspace`) |

> please mind that in this case the option `--here` is required for the `apax create`command

### Create a signed package with your own private key

//todo

## 10 Scripting with Apax

In the apax.yml you can define a scripting section.

Example:

```yml
scripts:
  my-script:
    - apax build
    - apax test
```

To execute the scripts just enter `apax my-script`. In the given example, `apax build` and `apax test` will executed.

### Build-In-Scripts

|             |                                                                                  |
| ----------- | -------------------------------------------------------------------------------- | --- | ------ | --------------------------------------------------- |
| prebuild    | executed before `apax build`                                                     |
| postbuild   | executed after `apax build`                                                      |
| prepack     | executed before `apax pack`                                                      |
| postpack    | executed after `apax pack`                                                       |
| preinstall  | executed before `apax [install                                                   | add | remove | update]`; --ignore-scripts disbables the execution. |
| postinstall | executed after `apax [install                                                    | add | remove | update]`; --ignore-scripts disbables the execution. |
| postcreate  | executed after `apax create`with the option `--postcreate`; disabled by default. |
