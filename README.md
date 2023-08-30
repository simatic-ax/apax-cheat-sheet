# Apax Cheet Sheet

## 01 Apax initial work

|||
|-|-|
|`apax login`|interactive login|
|`apax login --registry REGISTRY-URL [--password ACCESS-TOKEN]` |Login to a custom registry e.g. https://npm.pkg.github.com/|
|`apax self-update`| Update Apax itself.|

> a login might be required to install dependencies from registries, where a authentication is required

## 02 Setup workspaces

|command||
|-|-|
| `apax create` | create a new project interactively|
| `apax create TEMPLATE NAME` | |
| `apax create TEMPLATE --registry=custom-registry-url NAME` |Create  a workspace from a template, which is not on the default registry|

### Options

  |command|short||
  |-|-|-|
  |`apax create --postcreate`|`-p`| Run an existing `postcreate` script during creation.|
  |`apax create --here`|| Create the new project directly in the current directory, instead of using a new directory.|
  |`apax create --no-git`|| Skip the creation and initialization of a git repository.

## 03 Install dependencies

|command|short||
|-|-|-|
|`apax install --update-lockfile`| `-L` | Do not return with a non-zero exit code if updates to the apax-lock.json are required |
|`apax install  --copy-local`| `-c` |Copy packages instead of linking to the global cache.|
|`apax install  --redownload`| `-r` |Redownload packages, even if they are already available in the global cache. |

Dependencies will be installed according the semantic versioning

### Options

|command||
|-|-|
|`apax install --ignore-scripts` | Do not run preinstall and postinstall scripts.|

### Examples for `apax install`

```yml
devDependencies:
  "@ax/sdk": X.Y.Z #latest version 4.0.8
```
|`@ax/sdk": X.Y.Z` | installed version |
|-|-|
|`@ax/sdk": 4.0.2` | 4.0.2|
|`@ax/sdk": ^4.0.2`| 4.0.8|
|`@ax/sdk": 3.0.2` | 3.0.2|
|`@ax/sdk": ^3.0.2`| 3.0.19|

## 04 Update dependencies

### Commands

|command|short||
|-|-|-|
|`apax update`| | Interactive update of all dependencies according the semantic versioning.|
|`apax update PACKAGE`| | The `PACKAGE` that should be updated (non-interactive)|
|`apax update --all`| `-a` |Update all package (non interactive) according the semantic versioning|
|`apax update --forceLatest`| `-f` | Force latest version. |

### Options

### Example for `apax update`

```yml
devDependencies:
  "@ax/sdk": X.Y.Z  // latest version 4.0.8
```
|`@ax/sdk": X.Y.Z` | result |
|-|-|
|`@ax/sdk": 4.0.2` | No updates available
|`@ax/sdk": ^4.0.2`| @ax/sdk 4.0.2 -> 4.0.8
|`@ax/sdk": 3.0.2` | No updates available
|`@ax/sdk": ^3.0.2`| @ax/sdk 3.0.2 -> 3.0.19 (major version is pinned)

### Example for `apax update --forceLatest`

```yml
devDependencies:
  "@ax/sdk": x.y.z  // latest version 4.0.8
```

|entry in apax.yml| result |
|-|-|
|`@ax/sdk": 4.0.2`  | @ax/sdk 4.0.2 -> 4.0.8
|`@ax/sdk": ^4.0.2` | @ax/sdk 4.0.2 -> 4.0.8
|`@ax/sdk": 3.0.2`  | @ax/sdk 3.0.2 -> 4.0.8
|`@ax/sdk": ^3.0.2` | @ax/sdk 3.0.2 -> 4.0.8

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

|command|short||
|-|-|-|
|`apax add PACKAGE`| | The name of the package to add. It calls implicit an `apax install`|
|`apax add PACKAGE --Dev`| `-D` | Whether to add the package as a development dependency.|
|`apax remove PACKAGE`| | The name of the package to remove. |

### Further options

|command||
|-|-|
|`apax install --ignore-scripts` | Do not run preinstall and postinstall scripts.|

## 09 Typical workflows

### Create workspace for a PLC application, library or a TIAX workflow via CLI and open it in AxCode

||||
|-|-|-|
|1.| *open a command line window* |
|2.| `cd <destination directory>`| select the destination directory eg. `cd \temp\`|
|3.| `apax create TEMPLATE NAME`| TEMPLATE = [**app, lib, tiax**], NAME = **your workspace name** (e.g. `myWorkspace`) |
|4.| `cd NAME`| change to the directory e.g. `cd myWorkspace`|
|5.| `apax install -L`| install the dependencies |
|6.| `axcode .` | open AX Code |


### Create workspace for a PLC application, library or a TIAX workflow via CLI in an opened AxCode

||||
|-|-|-|
|1.| *AxCode is already open* |
|2.| *Select `File-->Close folder` if a folder is opened* |
|3.| *Open a terminal in AxCode* |
|4.| `cd <destination directory>`| select the destination directory eg. `cd \temp\`|
|5.| `apax create <TEMPLATE> <NAME> --here`| TEMPLATE = [**app, lib, tiax**], NAME = **your workspace name** (e.g. `myWorkspace`) |

> please mind that in this case the option `--here` is required for the `apax create`command
> 