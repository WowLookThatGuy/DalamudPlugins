# DalamudPlugins

Custom Dalamud plugin repository for WowLookThatGuy's plugins.

## Install

In game, open `/xlsettings` → **Experimental** → **Custom Plugin Repositories**, add

```
https://raw.githubusercontent.com/WowLookThatGuy/DalamudPlugins/main/repo.json
```

tick **Enabled**, save, then install from `/xlplugins`. Updates arrive automatically.

## Plugins

| Plugin | Source |
| --- | --- |
| Retainer Gear Optimizer | [WowLookThatGuy/RetainerGearOptimizer](https://github.com/WowLookThatGuy/RetainerGearOptimizer) |

## Publishing a plugin

`repo.json` is maintained by `.github/workflows/publish-plugin.yml`, a reusable workflow. A plugin repository publishes
here by calling it when a release is published there:

```yaml
name: Publish to plugin repository

on:
  release:
    types: [published]
  workflow_dispatch:
    inputs:
      tag:
        description: Release tag to publish
        required: true

permissions:
  contents: read

jobs:
  publish:
    uses: WowLookThatGuy/DalamudPlugins/.github/workflows/publish-plugin.yml@main
    with:
      internal-name: MyPlugin            # InternalName; the zip must contain MyPlugin.json
      release-tag: ${{ github.event.release.tag_name || inputs.tag }}
    secrets:
      PLUGIN_REPO_TOKEN: ${{ secrets.PLUGIN_REPO_TOKEN }}
```

Requirements for the plugin repository:

- Its release must have exactly one `.zip` attached: the DalamudPackager `latest.zip` (it contains `<InternalName>.json`).
- An Actions secret `PLUGIN_REPO_TOKEN`: a fine-grained personal access token limited to this repository with
  **Contents: Read and write**.

Icons live in `icons/<InternalName>.png` (square PNG, 64-512 px). Pass the raw URL of the icon as `icon-url` in the
plugin's publish workflow so it is kept in `repo.json` on every publish.

Each publish uploads the zip to a release tagged `<InternalName>-v<AssemblyVersion>` in this repository and replaces the
plugin's entry in `repo.json` with its manifest plus download links. Bump the plugin's version for every release, since Dalamud
only offers an update when `AssemblyVersion` increases.
