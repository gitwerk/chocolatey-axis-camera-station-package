# AXIS Camera Station Client Chocolatey Package
![Chocolatey Version](https://img.shields.io/chocolatey/v/axis-camera-station-client?label=chocolatey)
![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/gitwerk/chocolatey-axis-camera-station-package/main.yml)

This repository contains the source of a Chocolatey package that installs and updates the [AXIS Camera Station 5](https://www.axis.com/products/axis-camera-station) client (client only, no server). The installer is the official client MSI from Axis; the package downloads it and verifies its SHA256 checksum.

## Installation
```bash
choco upgrade axis-camera-station-client
```

### Package parameters
| Parameter | Effect |
|---|---|
| `/Telemetry` | Allow Axis to collect usage data (off by default) |
| `/DisableTls` | Let the installer turn off TLS 1.0/1.1 system-wide, after a reboot (off by default) |
| `/NoFirewall` | Don't create Windows Firewall rules |
| `/NoDesktopShortcut` | Don't create a desktop shortcut |

```bash
choco upgrade axis-camera-station-client --params "'/NoDesktopShortcut'"
```

The client is never started after a silent install. The installer language is detected from the system's regional settings. Chocolatey does not remember package parameters on upgrade unless `useRememberedArgumentsForUpgrades` is enabled:

```bash
choco feature enable -n useRememberedArgumentsForUpgrades
```

These parameters map to the MSI properties documented by Axis in [Installation parameters – AXIS Camera Station Microsoft installer](https://www.axis.com/dam/public/76/4b/d4/installation-parameters-microsoft-installer-en-GB+en-US-416222.pdf).

## Updating the package
Every Monday the `Check for New Version` workflow (`update.yml`) compares the packaged version with Axis's latest version. When Axis has a newer one, it downloads the MSI, updates `$version` and `$checksum` in `tools/chocolateyinstall.ps1`, and opens a pull request. Merging the pull request starts `Tag Release` (`release.yml`), which tags the version and runs the publish workflow. You can also run `Check for New Version` by hand.

The repository setting *Settings → Actions → General → Allow GitHub Actions to create and approve pull requests* must be enabled.

To update by hand:
1. Look up the new version in https://www.axis.com/ftp/pub_soft/cam_srv/cam_station/latest/ver.txt (e.g. `5.59.56000`). Axis publishes each release in a versioned folder such as `.../cam_station/5_59_56000/AXISCameraStationClient_5.59.56000.msi`.
2. Download the client MSI and compute its checksum:
   ```powershell
   Get-FileHash .\AXISCameraStationClient_5.59.56000.msi -Algorithm SHA256
   ```
3. Update `$version` and `$checksum` in `tools/chocolateyinstall.ps1`.
4. Commit and push to `master`:
   ```bash
   git commit -am "Update to 5.59.56000"
   git push
   ```

`Tag Release` then tags the version and publishes the package to Chocolatey. You can also run the publish workflow by hand (`workflow_dispatch`) with an existing tag.

## Contributing
Issues and pull requests are welcome.
