# Scoop Manifests

This repository contains Scoop manifest files for installing applications using the Scoop package manager on Windows.

## podman-compose

A Scoop manifest for installing [podman-compose](https://github.com/containers/podman-compose), a script to run docker-compose.yml using podman.

### Installation

To install podman-compose using this manifest:

```powershell
scoop install https://raw.githubusercontent.com/DilipPushparajan/scoop-manifests/main/podman-compose.json
```

Or add this repository as a Scoop bucket:

```powershell
scoop bucket add dilip-bucket https://github.com/DilipPushparajan/scoop-manifests
scoop install podman-compose
```

### Requirements

- Python must be installed (the manifest declares it as a dependency)
- Scoop package manager for Windows

### Manifest Structure

The manifest follows Scoop's standard format and includes:

- **version**: Current version of podman-compose (1.5.0)
- **url**: Download URL for the package from PyPI
- **hash**: SHA256 hash for integrity verification
- **depends**: Python dependency
- **installer**: Script to install the package using pip
- **post_install**: Script to make the executable available
- **uninstaller**: Script to uninstall the package
- **bin**: Binary/script to be added to PATH
- **checkver**: Automatic version checking from PyPI
- **autoupdate**: Automatic update configuration

### Changes Made

The manifest was corrected to follow proper Scoop standards:

1. **Added required fields**: `url` and `hash` fields for package download
2. **Fixed installer syntax**: Changed from array format with nested script objects to proper `installer.script` format
3. **Fixed license**: Changed from `GPL-2.0-or-later` to `MIT` to match the LICENSE file
4. **Updated version**: Updated from 1.0.8 to latest version 1.5.0
5. **Added post_install**: Script to properly place the executable in the bin directory
6. **Added bin field**: To make podman-compose available in PATH
7. **Added checkver and autoupdate**: For automatic version management
8. **Fixed depends format**: Changed from array to string format

### How It Works

1. Scoop downloads the tar.gz file from PyPI
2. The installer script uses pip to install the package into a local lib directory
3. The post_install script copies the executable from the Scripts directory to make it accessible
4. The bin field tells Scoop to add podman-compose to the PATH
5. The uninstaller script removes the package using pip

## License

MIT License - See LICENSE file for details
