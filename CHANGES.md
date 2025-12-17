# Changes to Scoop Manifest

## Summary

The original Scoop manifest for podman-compose was an attempt to use Scoop utility on Windows for installation, but it had several structural issues that prevented it from working correctly with Scoop. This document explains what was wrong and how it was fixed.

## Original Issues

### 1. Missing Required Fields
**Problem:** The manifest was missing critical fields required by Scoop:
- No `url` field (where to download the package)
- No `hash` field (for integrity verification)
- No `bin` field (to add the executable to PATH)

**Impact:** Scoop wouldn't know where to download the package from or how to verify its integrity.

### 2. Incorrect Installer/Uninstaller Syntax
**Problem:** The original used this structure:
```json
"installer": [
    {
        "script": "pip install podman-compose"
    }
]
```

**Why it's wrong:** Scoop expects either:
- `"installer": { "script": "..." }` (object with script property)
- `"installer": { "file": "...", "args": [...] }` (object with file and args)

The array-of-objects format with nested script was not valid Scoop syntax.

### 3. License Mismatch
**Problem:** Manifest declared `GPL-2.0-or-later` but the LICENSE file contains MIT license.

**Impact:** Incorrect license information could cause legal or compliance issues.

### 4. Outdated Version
**Problem:** Manifest specified version 1.0.8 (from 2020), while the latest stable version is 1.5.0 (2024).

**Impact:** Users would install an outdated, potentially buggy version.

### 5. Missing Version Management
**Problem:** No `checkver` or `autoupdate` fields.

**Impact:** Manual version updates would be required; no automatic version checking.

### 6. Incomplete Installation
**Problem:** The installer would run `pip install podman-compose` but wouldn't properly make the executable accessible to Scoop.

**Impact:** Even if installed, the podman-compose command might not work correctly.

## What Was Fixed

### 1. Added Required Fields ✅
- **url**: Points to the tar.gz file on PyPI
- **hash**: SHA256 hash for integrity verification
- **bin**: Tells Scoop to add podman-compose to PATH

### 2. Fixed Installer Syntax ✅
Changed from:
```json
"installer": [{ "script": "pip install podman-compose" }]
```

To proper Scoop format:
```json
"installer": {
    "script": "python -m pip install --no-warn-script-location --target \"$dir\\lib\" \"$dir\\$fname\""
}
```

Key improvements:
- Uses `python -m pip` for better compatibility
- Installs to local `lib` directory (isolated installation)
- Uses `$fname` variable to reference downloaded file
- Adds `--no-warn-script-location` to suppress pip warnings

### 3. Added post_install Script ✅
```json
"post_install": [
    "if (Test-Path \"$dir\\lib\\Scripts\\podman-compose.exe\") {",
    "    Copy-Item \"$dir\\lib\\Scripts\\podman-compose.exe\" \"$dir\\podman-compose.exe\" -Force",
    "} elseif (Test-Path \"$dir\\lib\\Scripts\\podman-compose\") {",
    "    Copy-Item \"$dir\\lib\\Scripts\\podman-compose\" \"$dir\\podman-compose\" -Force",
    "}"
]
```

This ensures the executable is properly placed where Scoop can find it.

### 4. Fixed License ✅
Changed from `GPL-2.0-or-later` to `MIT` to match the actual LICENSE file.

### 5. Updated to Latest Version ✅
Updated from 1.0.8 to 1.5.0 (latest stable release).

### 6. Added Version Management ✅
```json
"checkver": {
    "url": "https://pypi.org/pypi/podman-compose/json",
    "jsonpath": "$.info.version"
},
"autoupdate": {
    "url": "https://files.pythonhosted.org/packages/source/p/podman-compose/podman_compose-$version.tar.gz"
}
```

This enables:
- Automatic version checking from PyPI
- Automatic URL updates when new versions are released

### 7. Fixed depends Format ✅
Changed from `"depends": ["python"]` to `"depends": "python"` (simpler string format for single dependency).

## How the Fixed Manifest Works

1. **Download**: Scoop downloads the tar.gz file from PyPI using the `url` field
2. **Verify**: Scoop verifies the download using the SHA256 hash
3. **Install**: The installer script uses pip to install the package into a local lib directory
4. **Post-Install**: The post_install script copies the executable to make it accessible
5. **PATH**: The bin field tells Scoop to add podman-compose to the user's PATH
6. **Updates**: The checkver/autoupdate fields allow for automatic version checking

## Testing the Manifest

To test if the manifest works correctly on Windows with Scoop installed:

```powershell
# Install from the manifest
scoop install https://raw.githubusercontent.com/DilipPushparajan/scoop-manifests/main/podman-compose.json

# Verify it works
podman-compose --version

# Uninstall
scoop uninstall podman-compose
```

## Standards Followed

The corrected manifest follows these Scoop best practices:

1. ✅ Uses proper JSON structure
2. ✅ Includes all required fields (version, description, homepage, license, url, hash)
3. ✅ Uses correct installer/uninstaller syntax
4. ✅ Properly handles PATH via bin field
5. ✅ Includes version checking (checkver)
6. ✅ Supports automatic updates (autoupdate)
7. ✅ Uses isolated installation (pip --target)
8. ✅ Handles executable placement correctly

## References

- [Scoop Documentation](https://github.com/ScoopInstaller/Scoop/wiki)
- [App Manifests](https://github.com/ScoopInstaller/Scoop/wiki/App-Manifests)
- [PyPI podman-compose](https://pypi.org/project/podman-compose/)
