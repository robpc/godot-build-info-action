# Godot Build Info

Adds settings for Godot projects at build time related to the build.

# Project Settings

Settings added/changed by this action:

```
build_info/package/version
build_info/package/build_date
build_info/source/commit
```

The action uses the `override.cfg` file to add the build settings to the
project. It is assumed that either the `override.cfg` either
doesn't exist or the `[build_info]` section is not present. It
is ok to add these to the Project Settings for testing as the
`override.cfg` will override them. The settings can be accessed like
any other Project Setting using `ProjectSettings.get_setting(name)`, etc. See the [Godot Project Setting Documentation](https://docs.godotengine.org/en/stable/classes/class_projectsettings.html) for more info.

# Inputs

| Input name | Default               | Description                          |
| ---------- | --------------------- | ------------------------------------ |
| version    | ''                    | package version                      |
| commit     | `$GITUB_SHA`          | commit hash                          |
| build_date | Generated from `date` | Date/Time when the package was built |
| file       | `override.cfg`        | File to add the settings             |

Each input can be used to override the related setting, but all come with
sensible defaults, except `version` which defaults to `''`.

# Example

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v2
        with:
          lfs: true
      - run: echo "version=${GITHUB_REF/refs\/tags\/v/}" >> $GITHUB_ENV
      - name: Add Build Info
        uses: robpc/godot-build-info-action@v1
        with:
          version: ${{ env.version }}
      - name: Export ${{ matrix.preset }} from Godot
        uses: robpc/godot-export-action@v3.3.1
        with:
          preset: windows
          export_path: build/my-game-v${{env.version}}.exe
```