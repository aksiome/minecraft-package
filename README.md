# Package Minecraft Java World

[![Source Code](https://img.shields.io/badge/source-aksiome/minecraft--package-4078C0.svg?style=flat-square&labelColor=555555&logo=github)](https://github.com/aksiome/minecraft-package)
[![Software License](https://img.shields.io/github/license/aksiome/minecraft-package?style=flat-square)](https://github.com/aksiome/minecraft-package/blob/master/LICENSE)

Github action to automatically prepare a Minecraft World for release.

## Usage
This github action uses [`mcwpack`](https://github.com/aksiome/mcwpack) and needs an additional yaml config file inside the repository.

### Inputs

| Input     | Optional                   | Description                       |
| --------- | -------------------------- | --------------------------------- |
| `source`  | required                   | The world directory to compress   |
| `target`  | required                   | The target zip file               |
| `config`  | defaults to `mcwpack.yaml` | The mcwpack config file           |
| `version` | defaults to `latest`       | The version of mcwpack to install |

### Outputs
`archive` - Path to the archive containing the compressed world

### Config

Default `mcwpack.yaml`:

```yaml
# Level name (supports minecraft color codes)
name: null
# Directory name inside the archive
dirname: null
# Resourcepack directory (or zip archive)
resourcepack: null
# Reset player data in level.dat
reset_player: true
# Archive all non-archived datapacks
zip_datapacks: true
# Remove empty chunks from the world
clean_chunks: true
# Ignored blocks when cleaning chunks (default: minecraft:air)
# The following will delete chunks that only contain air or stone
# ignored_blocks:
#   - minecraft:air
#   - minecraft:stone
# Filter accepted scores (using glob patterns)
# accepted_scores:
# Filter accepted objectives (using glob patterns)
# accepted_objectives:
# Filter accepted file entries (using glob patterns)
accepted_entries:
  - data/*.dat
  - datapacks/*
  - poi/*.mca
  - region/*.mca
  - entities/*.mca
  - icon.png
  - level.dat
```

## Examples
Automatically create a release. Uses [`softprops/action-gh-release`](https://github.com/softprops/action-gh-release):
```yml
name: Release
on:
  push:
    tags:
      - '*'
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Package
        id: package
        uses: aksiome/minecraft-package@master
        with:
          source: path/to/my_world
          target: my_compressed_world.zip
      - name: Release
        uses: softprops/action-gh-release@v1
        with:
          files: ${{ steps.package.outputs.archive }}
```

Upload output as an artifact. Uses [`actions/upload-artifact`](https://github.com/actions/upload-artifact):
```yml
name: Build
on: push
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Package
        id: package
        uses: aksiome/minecraft-package@master
        with:
          source: path/to/my_world
          target: my_compressed_world.zip
      - name: Upload artifact
        uses: actions/upload-artifact@v3
        with:
          name: My World
          path: ${{ steps.compile.outputs.archive }}
```
