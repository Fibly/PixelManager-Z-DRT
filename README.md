# PixelManager + Z-DRT

This is a fork of [Joegenco/PixelManager](https://github.com/Joegenco/PixelManager), based specifically on the official [v.2.0-RC6 release tag](https://github.com/Joegenco/PixelManager/tree/v.2.0-RC6) at commit `d3ca750dc964ec75e66d9b213b1a251e94f300b4`.

The Z-DRT branch starts from RC6; it does not include the later changes on upstream `master`.

**Z-DRT release status: in preparation.** The final PixelManager + Z-DRT folder is not ready, and no Z-DRT release package has been uploaded. The config and LUTs in this branch are currently the unmodified RC6 files.

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
