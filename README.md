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

The original PixelManager documentation and source credits are preserved below.

---

# PixelManager OCIO config
Tries to manage pixels colors

This is not a full readme. That is coming soon™

Tested with "Blender 4.5+, Maya 2023+, Nuke 15+, Fusion 18.5+, Substance 2025 and more...

## How to edit "Colorspaces" visible to users (Inactive Colorspaces)
To be able to see and utilize more colorspaces, open `config.ocio` file
then go to **line 296** and enable of the `inactive colorspaces` by removing the comment (`#`). Use the default `Minimal` preset as an example.

Only one `inactive_colorspaces` line can be active at a time.

#### Avalilabe presets: 
 - `Minimal`: Inactive spaces with absolute minimal clutter
 - `CG`: Inactive spaces with STORAGE log formats visible
 - `STUDIO`: Inactive spaces with CAMERA log formats visible to the user
 - `Minimal-Comp-Grading (Mainly for Blender) sRGB`: All but sRGB image formation inactive. No camera LOG spaces visible. Useful when forming images in compositor (For sRGB displays)
- `STUDIO-Comp-Grading (Mainly for Blender) sRGB` : All but sRGB image formation inactive. Camera LOG spaces are visible. Useful when forming images in compositor (For sRGB displays)

*If youre using it with **Fusion**, comment out all of them as Fusion hides all* 
## DCTL's and source configs

 - [Troy James Sobotka's AgX (Kraken)](https://github.com/sobotka/AgX)
- [Troy James Sobotka's AgX Resolve DCTL](https://github.com/sobotka/AgX-Resolve)
- [Eary Chow's AgX (Default Blender)](https://github.com/EaryChow/AgX) 
 - [Juan Pablo Zambrano's DRT2399 - DCTL](https://github.com/JuanPabloZambrano/DCTL)
 - [Jedy Pod's OpenDRT & JzDT - DCTL](https://github.com/jedypod/open-display-transform)
 - [Filmlight's TCAMv2 and TCAMv3](https://www.filmlight.ltd.uk/support/customer-login/colourspaces/colourspaces.php)
 - [ACES 1.3 for OCIO 2.0](https://github.com/AcademySoftwareFoundation/OpenColorIO-Config-ACES/releases/)
 - [ARRI image formation]( https://www.arri.com/en/learn-help/learn-help-camera-system/tools/lut-generator)
  - [RED IPP2 image formation]( https://www.red.com/download/ipp2-output-presets)
  - [Sony image formation]( https://pro.sony/en_ME/product-resources/knowledge-panel/s709-monitor-look-white-paper)
  - [AgX flim]( https://github.com/bean-mhm/flim)
  - [Unreal Engine DCTL]( https://github.com/mlleemiles/UE5_ACES_DCTL)
  

-----------------
