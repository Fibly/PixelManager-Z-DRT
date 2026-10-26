# PixelManager + Z-DRT

An extended OCIO configuration based on the official [PixelManager v.2.0-RC6 release](https://github.com/Joegenco/PixelManager/tree/v.2.0-RC6), commit `d3ca750dc964ec75e66d9b213b1a251e94f300b4`. RC6 is the starting point for this fork rather than the current upstream `master` branch.

**Z-DRT's Blender compositor and original transform were created by [Zircron45](https://github.com/Zircron45). [Fibly](https://github.com/Fibly) converted that compositor into an OCIO transform and integrated it into this PixelManager fork.**

The project adds Z-DRT and other transforms absent from RC6, updates selected existing transforms, and prepares separate legacy and modern OCIO editions.

**Release packages are in preparation.** Neither edition has been uploaded or published. The repository's runtime files currently contain the RC6 baseline; the additions described below are being prepared for the finished packages. Downloads will appear under [GitHub Releases](https://github.com/Fibly/PixelManager-Z-DRT/releases).

- [Planned editions and additions](#planned-editions-and-additions)
- [Creation process](#creation-process)
- [Development tools and commands](#development-tools-and-commands)
- [Credits and transform sources](#credits-and-transform-sources)
- [Software and reference resources](#software-and-reference-resources)

## Planned editions and additions

| Edition | Intended hosts | Configuration |
|---|---|---|
| **Legacy compatibility** | Applications using older OCIO 2.x libraries | Profile 2.0, with the extended transform collection expressed using compatible OCIO operators. |
| **Blender 5.2 / modern OCIO** | Blender 5.2 and other hosts using OCIO 2.5 or newer | Profile 2.5, with native output transforms, display integration, and HDR-related metadata. |

The development configs are named `config.ocio` and `config_v2.5.ocio`. Choose an edition according to the OCIO version supported by the application. The final archives and their release notes will identify their supported configurations and included assets.

The additions and updates being prepared include:

- **Z-DRT**, converted from Zircron45's compositor into an OCIO display view. The current reference targets the sRGB display and its pure gamma-2.2 encoding; it is not an HDR or P3 Z-DRT reference.
- **flim 1.2.0**, including **flim (nostalgia)** alongside the default and silver views.
- **Native ACES 2.0 output transforms** in the modern edition. The existing Resolve-baked **ACES 2** views remain distinct.
- Blender integration through matched color-space identifiers, encoding metadata, and modern display/view separation.
- Selected fixes to Khronos Neutral on sRGB and AgX Kraken view routing, plus corrected Sony s709 naming. These changes are applied individually to the RC6 base.

## Creation process

Z-DRT's appearance comes from Zircron45's Blender compositor. The conversion samples that existing color function and approximates it with an OCIO transform; it does not claim authorship of the original look.

1. **Pin the inputs.** Start from PixelManager's RC6 tag, Zircron45's source `.blend`, the exact OCIO configuration expected by that graph, the flim 1.2.0 assets, and Blender's bundled color-management data. Record the source hashes, Blender build, OCIO version, and parameters used for a build.
2. **Inspect the compositor.** Follow the active output path and check the working space and view settings. The bake requires a per-pixel color function: blur, glare, and other operations that depend on neighboring pixels cannot be represented by a single RGB LUT. Unexpected exposure, gamma, looks, curves, missing color spaces, and unsupported active spatial nodes are rejected.
3. **Evaluate the original graph.** Feed known float RGB values through Blender's compositor and record float EXR results. The development workflow uses full-precision evaluation, identity calibration, and checks for non-finite values. Reference evaluation and the final OCIO processor are separate paths.
4. **Choose sampling coordinates.** The current development reference uses an 89 x 89 x 89 lattice. Coordinates pass through AP0, a reversible 20% desaturation matrix, a base-2 log shaper with a linear toe through zero, normalization and a supported-range clamp, then HSV. Samples are mapped back through the inverse coordinate mapping before evaluating the graph. These operations organize the sampling domain; the table accounts for that mapping.
5. **Fit the LUT.** Regularized least squares adjusts lattice values to reduce error between sample points under tetrahedral interpolation. Neutral and black constraints preserve those reference values, and the two hue-wrap faces are tied together. This is numerical fitting, not neural-network training. The current reference stores final gamma-2.2 display values for sRGB primaries; that encoding is different from the piecewise IEC sRGB curve.
6. **Build the two configs.** Construct a clean legacy config from RC6, apply the selected additions and fixes, then install the Z-DRT view, shaper, LUT, and manifest. Generate the modern config from that completed legacy config, adding native OCIO 2.5 identifiers, display/view structure, and native ACES 2.0 outputs.
7. **Validate the packaged result.** Compare the actual OCIO processor against the compositor on held-out colors and images. Check black, neutrals, saturated colors, zero crossings, and the hue wrap; record average, percentile, and worst-case errors. Compare existing PixelManager views and test the relevant OCIO versions. Actual CPU, GPU, viewport, and host checks are recorded separately; loading a config or generating a shader does not establish every runtime result.
8. **Assemble a release.** Package each config with all of its required assets and notices. Record source and output hashes, lattice/shaper settings, tool versions, and validation results in the release manifest and notes. Experimental candidates remain separate until accepted. The final release folder is still being prepared.

The `.cube` is only one part of Z-DRT. It needs its matching OCIO shaper, color-space definitions, and output encoding; loading it as an ordinary scene-linear cube gives a different result. A finite LUT is an approximation, and a lower average error alone does not establish that rare artifacts are acceptable.

Development and review of the conversion tooling have used AI assistance. The bake itself runs locally through Blender, OpenColorIO, and numerical fitting, without calling a generative AI service. This tooling assistance is separate from Zircron45's compositor authorship and Fibly's conversion work.

## Development tools and commands

These tools currently exist in the development workspace. They and Zircron45's source `.blend` have **not yet been uploaded to this repository**, so the present checkout is not a complete rebuild environment.

| Tool | Role |
|---|---|
| `tools/pixelmanager_build.py` | Build the RC6-based legacy config and generate the modern config. |
| `tools/zdrt_blender_worker.py` | Inspect and evaluate the original Blender compositor. |
| `tools/zdrt_lattice.py` | Sampling coordinates, tetrahedral interpolation, constraints, and numerical fitting. |
| `tools/zdrt_pipeline.py` | Bake, install, and validate the Z-DRT package. |
| `tools/pixelmanager_verify.py` | Compare actual display/view outputs between configs. |
| `tools/zdrt_ocio_compat.py` | Check config loading, processors, and GPU shader generation across OCIO versions. |

The command outline below uses the development tools. Set `$BlenderPython` to the matching Blender build's bundled Python with NumPy, PyOpenColorIO, and OpenImageIO, and `$BlenderExe` to that build's Blender executable. Set the input paths to the pinned RC6 archive, extracted flim assets, Blender config, source `.blend`, and the unchanged reference OCIO config used by the graph. Use a **new** `$PackageDir` and `$WorkDir`. The package step requires a complete config and assets without an existing Z-DRT view.

```powershell
& $BlenderPython -I tools/pixelmanager_build.py legacy `
  --rc6-zip $RC6Archive --flim $FlimAssets `
  --blender-config $BlenderConfig --out $PackageDir

& $BlenderPython -I tools/zdrt_pipeline.py bake `
  --blend $SourceBlend --oracle-config $ReferenceConfig `
  --work $WorkDir --sizes 89 --blender $BlenderExe

& $BlenderPython -I tools/zdrt_pipeline.py package `
  --work $WorkDir --size 89 --config "$PackageDir/config.ocio"

& $BlenderPython -I tools/pixelmanager_build.py modern `
  --package $PackageDir --blender-config $BlenderConfig

& $BlenderPython -I tools/zdrt_pipeline.py validate `
  --work $WorkDir --config "$PackageDir/config.ocio" --blender $BlenderExe

& $BlenderPython -I tools/zdrt_pipeline.py validate `
  --work $WorkDir --config "$PackageDir/config_v2.5.ocio" --blender $BlenderExe
```

The bake is a substantial numerical job. These commands describe the current development workflow; this README update does not represent a new bake, a finalized release, or acceptance of experimental candidates. Final release records will identify the exact source and parameter set used.

## Credits and transform sources

The acknowledgements below cover the named creators and sources identified in the RC6 config, its source list, and this conversion's inputs. Project links identify provenance; they do not imply that the latest version from each project has been included.

| Component | Credit and resources |
|---|---|
| **Original Z-DRT** | **[Zircron45](https://github.com/Zircron45)** — Blender compositor, original transform, and appearance. |
| **OCIO conversion and this fork** | **[Fibly](https://github.com/Fibly)** — conversion of Zircron45's compositor, PixelManager integration, and release editions. |
| PixelManager RC6 | **Genco Uney / [Joegenco](https://github.com/Joegenco)**, with contributions credited to **Nihal Rahman**. [RC6 source](https://github.com/Joegenco/PixelManager/tree/v.2.0-RC6) and [config credits](https://github.com/Joegenco/PixelManager/blob/v.2.0-RC6/config.ocio). |
| Filmic, original AgX / Kraken, AgX-Resolve, and SB2383 work | **Troy James Sobotka**. [Filmic](https://github.com/sobotka/filmic-blender), [AgX](https://github.com/sobotka/AgX), [AgX-Resolve](https://github.com/sobotka/AgX-Resolve), and [SB2383 configuration generation](https://github.com/sobotka/SB2383-Configuration-Generation). |
| Blender/Eary AgX variant | **Eary Chow**, **Mark Faderbauer**, and **Sakari Kapanen**, as credited by the inherited config. [EaryChow/AgX](https://github.com/EaryChow/AgX). |
| flim / grace | **Hadi Farid (Bean)**. [flim source](https://github.com/bean-mhm/flim) and [releases](https://github.com/bean-mhm/flim/releases). The conversion uses the 1.2.0 assets. |
| OpenDRT and JzDT | **Jed Smith / Jedy Pod**. [open-display-transform](https://github.com/jedypod/open-display-transform). |
| JP-2499DRT | **Juan Pablo Zambrano**. [DCTL sources](https://github.com/JuanPabloZambrano/DCTL). The inherited version comes from RC6. |
| TCAMv2 and TCAMv3 | **FilmLight**. [Truelight Colour Spaces resources](https://www.filmlight.ltd.uk/support/customer-login/colourspaces/colourspaces.php). |
| Khronos PBR Neutral | **Khronos Group and contributors**. [ToneMapping sources](https://github.com/KhronosGroup/ToneMapping). |
| ACES and OCIO ACES configs | **Academy of Motion Picture Arts and Sciences**, **Academy Software Foundation**, and their contributors. [ACES core](https://github.com/ampas/aces-core) and [OpenColorIO-Config-ACES](https://github.com/AcademySoftwareFoundation/OpenColorIO-Config-ACES). The RC6 header identifies [config release v2.0.0](https://github.com/AcademySoftwareFoundation/OpenColorIO-Config-ACES/releases/tag/v2.0.0) for ACES 1.3. |
| ARRI image-formation LUTs | **ARRI**. [LUT generator](https://www.arri.com/en/software/postproduction-software/legacy/lut-generator). |
| Blackmagic Gen 5 Film to Extended Video and Resolve-baked ACES 2 | **Blackmagic Design**. [Camera resources](https://www.blackmagicdesign.com/support/family/professional-cameras) and [DaVinci Resolve](https://www.blackmagicdesign.com/products/davinciresolve). RC6's header records its ACES 2 LUTs as generated through Resolve 20.1; native ACES 2.0 is a separate modern-edition path. |
| Canon Cinema Gamut / Canon Log 3 LUTs | **Canon**. The inherited assets identify Canon LUT version 2.0, dated 2019-10-24. [Canon Log technical reference](https://www.usa.canon.com/content/dam/canon-assets/white-papers/pro/white-paper-canon-log-gamma-curves.pdf) and [current Canon LUT resources](https://color.ssw.imaging-saas.canon/color/en/pc/log/). |
| RED IPP2 image formation | **RED Digital Cinema**. [IPP2 output presets](https://www.reddigitalcinema.com/download/ipp2-output-presets). |
| Sony s709 / sP3 monitor-look LUTs | **Sony**. [s709 Monitor Look resources](https://pro.sony/en_ME/product-resources/knowledge-panel/s709-monitor-look-white-paper). |
| Unreal Engine viewport approximation | **mlleemiles**. [UE5 ACES DCTL](https://github.com/mlleemiles/UE5_ACES_DCTL). |

The inherited Filmic acknowledgements also name **Guillermo, Claudio Rocha, Bassam Kurdali, Eugenio Pignataro, Henri Hebeisen, Jason Clarke, Haarm-Peter Duiker, Thomas Mansencal, and Timothy Lottes** for feedback and contributions. These credits are preserved here without carrying over the upstream README's installation instructions or compatibility claims.

Upstream code, LUTs, and other assets retain their respective copyright and license terms. The final archives will need their applicable notices alongside these credits; this README does not assign a new license to third-party work.

## Software and reference resources

- **Blender Foundation and Blender contributors:** compositor evaluation, Python/GPU APIs, and bundled color-management data. [Blender](https://www.blender.org/), [color-management manual](https://docs.blender.org/manual/en/latest/render/color_management.html), and [Python API](https://docs.blender.org/api/current/).
- **OpenColorIO and its contributors / Academy Software Foundation:** OCIO operators, config processing, and GPU shader generation. [Project](https://opencolorio.org/) and [OCIO 2.0 transform API](https://opencolorio.readthedocs.io/en/v2.0.0/api/transforms.html).
- **NumPy contributors:** array processing and numerical fitting. [NumPy](https://numpy.org/).
- **OpenImageIO contributors / Academy Software Foundation:** float image input/output and reference EXR handling. [OpenImageIO](https://github.com/AcademySoftwareFoundation/OpenImageIO).
- **Python Software Foundation and Python contributors:** development scripting. [Python](https://www.python.org/).
- **OpenEXR sample-image authors and Academy Software Foundation:** external validation material, with the dataset's own per-image credits and notices. [Pinned openexr-images source](https://github.com/AcademySoftwareFoundation/openexr-images/tree/e38ffb0790f62f05a6f083a6fa4cac150b3b7452). Development records identify `Chromaticities/Rec709.exr`, `ScanLines/Carrots.exr`, and `TestImages/WideColorGamut.exr`; their interpreted color spaces are recorded with the validation data.
- **Attribution reference:** [PixelManager RC6's original source list](https://github.com/Joegenco/PixelManager/blob/v.2.0-RC6/README.md). This is a provenance reference, not installation guidance for this fork.
