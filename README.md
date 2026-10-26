# PixelManager + Z-DRT

This is a fork of [Joegenco/PixelManager](https://github.com/Joegenco/PixelManager), based specifically on the official [v.2.0-RC6 release tag](https://github.com/Joegenco/PixelManager/tree/v.2.0-RC6) at commit `d3ca750dc964ec75e66d9b213b1a251e94f300b4`. The release baseline is RC6 rather than upstream `master`.

The project extends RC6 with **Z-DRT and additional color and display transforms that were not included in the original PixelManager RC6 release**. It also updates existing transforms and adds compatibility and host-integration fixes.

## Planned release editions

Two editions are planned from the same RC6 foundation:

| Edition | Target | Purpose |
|---|---|---|
| **Legacy compatibility** | Applications using older OCIO 2.x versions; config profile 2.0 | Provides the extended transform collection in a config compatible with older OCIO hosts. |
| **Blender 5.2 / modern OCIO** | Blender 5.2 and other hosts using OCIO 2.5 or newer; config profile 2.5 | Uses newer OCIO features for native output transforms, display integration, and HDR support. |

Choose the edition according to the OCIO version supported by your application. Features that require OCIO 2.5 are available in the modern edition.

Planned transform additions and updates include:

- **Z-DRT**, added as a display view.
- **flim 1.2.0**, including the new **flim (nostalgia)** view.
- **Native ACES 2.0 output transforms** in the modern edition, alongside the existing LUT-based **ACES 2** view.

## Release status

**Release packages are in preparation.** The final PixelManager + Z-DRT folder is not ready, and neither edition has been uploaded or published. The config and LUTs currently in this repository are the RC6 baseline; the additions described above will be included with the finished packages.

Finished packages will be available from [GitHub Releases](https://github.com/Fibly/PixelManager-Z-DRT/releases) when they are ready.
