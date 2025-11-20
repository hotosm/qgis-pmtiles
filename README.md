# QGIS PMTiles Plugin

- GDAL can't handle raster PMTiles as of yet (last checked 23/10/2025).
- As a consequence QGIS does not have support for reading or writing
  raster PMTile files.
- This plugin is an interim solution to convert MBTiles file into
  PMTiles, using the official Python package.
- This plugin uses [QPIP](https://github.com/opengisch/qpip) for dependency management.
  The `pmtiles` Python package will be automatically installed when the plugin is loaded.

> [!NOTE]
> Originally, we preferred to vendor a version of the small PMTiles Python package
> inside this plugin, to ensure it works consistently into the future.
>
> This idea was rejected by the QGIS plugin reviewers, so instead QPIP is used.
>
> If your version of PMTiles installed doesn't work well with the plugin, sorry!

## Testing ogr2ogr & GDAL

```bash
ogr2ogr -if "MBTiles" info ./myfile.mbtiles --debug on

# Results in:
# SQLite: 1 features read on layer 'SELECT'.
# SQLite: 8 features read on layer 'SELECT'.
# MBTiles: This files contain raster tiles, but driver open in vector-only mode
# ERROR 1: Unable to open datasource `./bing_basemap.mbtiles' with the following drivers.

# Can't read mbtiles, so no pmtiles conversion
```

```bash
gdal convert --input myfile.mbtiles --output myfile.pmtiles --output-format "PMTiles"

# Results in:
# ERROR 1: convert: Invalid value for argument 'output-format'. Driver 'PMTiles' does not expose the required 'DCAP_RASTER' capability.
```

## Options available

- Tippecanoe (too heavy to bundle in QGIS / may cause licensing issues).
- go-pmtiles (hard to bundle different OS binaries in a plugin).
- Direct Python library usage (this plugin).

> ![NOTE]
> The `go-pmtiles` binary is much more efficient than the Python library.
>
> If we find an elegant solution for bundling the binary inside the
> QGIS Plugin, that could be considered too.
>
> Alternatively, I assume the C++ code in this repo could be used
> to make a C++ plugin for QGIS too.

## Building and Releasing

This plugin uses [qgis-plugin-ci](https://github.com/opengisch/qgis-plugin-ci) for automated building and releasing.

### Automated Release (Recommended)

Releases are handled automatically by GitHub Actions when you create a release on GitHub. The workflow will:
- Build the plugin package
- Upload it to the QGIS Plugin Repository
- Create a GitHub release

To release:
1. Update the version in `pyproject.toml`
2. Update the version in `PMTiles/metadata.txt`
3. Create a git tag: `git tag 0.1.0` (matching the version)
4. Push the tag: `git push origin 0.1.0`
5. Create a GitHub release from the tag

### Local Building

For local testing, you can use qgis-plugin-ci directly:

```bash
uv sync
uv run qgis-plugin-ci <version_number>
```

## Linting

```bash
uv sync
uv run ruff format
uv run ruff check
```
