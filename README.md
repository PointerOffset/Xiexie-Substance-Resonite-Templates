# Xiexie Toon Substance Painter Templates
#### **Author:** *Spex* (aka: *Pointer Offset*)
#### **Essential Supporting Research:** *Gareth48*, *Hayden*
Investigations by Gareth48 and Hayden were essential to bringing this together. Much of the credit for this work belongs to them. I just put the pieces together.

The Xiexie Toon Material in Resonite has a number of useful features that can end up under-utilized without some help with the workflow and documentation. I figured it was a good time to help fix that. Here I've provided template files that can be used in Substance Painter to assist in authoring textures, masks, and maps for the Xiexie Toon Material in Resonite. I make no claims about how useful this may be for authoring Xiexie-based materials in other contexts. Even if you don't use Substance Painter, this information may be useful for creating Xiexie Toon textures in other graphics software.

These templates **do not include any kind of custom shader** to preview their apperance in Substance Painter. They only assist is creating a complete texture set. You'll still need to check your work in-engine.

This information may not be 100% accurate so please feel free to file issues for corrections. 

### ℹ *Compatability Note*
> *These templates were authored using Substance 3D Painter, version 9.0.0 (Build 2585). I make no guarentees these will work in any other versions. It should work in newer versions. Your milage maybe vary with older versions.*

## Xiexie Toon.spt
This is the template file used when creating a new Substance Painter project. It sets up a template with appropraite channels. You should copy this to the following directory:

> *If the `templates` directory doesn't exist you may need to create it.*

| OS | File Path |
| -- | --------- |
| Windows | `%HOMEPATH%\Documents\Adobe\Adobe Substance 3D Painter\assets\templates` |
| Mac OS | `~/Documents/Adobe/Adobe Substance 3D Painter/assets/templates` |

 When used you'll end up with a Substance Painter project that has the following channels:

| Channel                     | Color Space    | Purpose                                                                                                       |
|-----------------------------|----------------|---------------------------------------------------------------------------------------------------------------|
| `Base Color`                | sRGB (8-Bit)   | Base color/Albedo                                                                                             |
| `Metallic`                  | Linear (8-Bit) | Metalness value of a given pixel.                                                                             |
| `Glossiness`                | Linear (8-Bit) | Glossiness/Smoothness value of a given pixel.                                                                 |
| `Normal`                    | RGB (16-Bit F) | Normal (facing direction) value of a given pixel.                                                             |
| `Opacity`                   | Linear (8-Bit) | Opacity/Transparency of a given pixel.                                                                        |
| `Emissive`                  | sRGB (8-Bit)   | Emissive color of a given pixel. Tinted by Emission color value on the material.                              |
| `Shadow Ramp Mask (User 0)` | Linear (8-bit) | Greyscale mask that selects which pixel row of the Shadow Ramp texture to use for a given pixel. See below.   |
| `Outline Mask (User 1)`     | Linear (8-Bit) | Outline thickness value for a given pixel. Multiplied by the overall Outline Thickness value of the material. |

`OcclusionMap` and `ThicknessMap` are not included as channels by default. They are expected to be generated from Baked Mesh Maps in Substance Painter. You can, of course, override this yourself in your project if desired.

## Xiexie Toon.spexp
This is the export template used when exporting your textures for use in-engine. All images are exported as *webp* in 8-Bit color by default. The normal map is 8-bit + dithering which is how normal maps are exported for the standard Metallic shader too. You should also set your `Prefered Profile` color space according to the table below in-game by inspecting your texture.

You should copy this to the following directory:
> *If the `export-presets` directory doesn't exist you may need to create it.*

| OS | File Path |
| -- | --------- |
| Windows | `%HOMEPATH%\Documents\Adobe\Adobe Substance 3D Painter\assets\export-presets` |
| Mac OS | `~/Documents/Adobe/Adobe Substance 3D Painter/assets/export-presets` |

| Texture Name                     | Color Channels        | In-Game `Prefered Profile` Setting |
|----------------------------------|-----------------------|------------------------------------|
| `MainTexture_AlbedoTransparency` | RGB+A                 | sRGB                               |
| `MetallicGlossMap`               | R: Metallic, A: Gloss | Linear                             |
| `ShadowRampMask`                 | Red                   | Linear                             |
| `OcclusionMap`                   | Red                   | Linear                             |
| `OutlineMask`                    | Red                   | Linear                             |
| `ThicknessMask`                  | Red                   | Linear                             |
| `EmissionMap`                    | RGB+A                 | sRGB*                              |
| `Normal`                         | RGB                   | Linear                             |

### ℹ *\*Color Space Note*
> *The mask/map textures should be interpreted as linear since they represent raw data, not color. The Main Texture should be sRGB. Emission will usually be sRGB. **However**, if you are making an emission map with values greater than 1.0, you should change the format to `exr` with `16f Bits` formatting and set the texture's `Prefered Profile` to `Linear` in-game.  Keep this information in-mind if you do so.*

## What is `Shadow Ramp Mask`?
`Shadow Ramp Mask` isn't immediately intuitive. It's more accurately called a "Shadow Ramp *Selection* Mask".

What is it that's being "selected"? The first thing to understand is the Shadow Ramp only samples a *single row of pixels* from your Shadow Ramp texture. Without a `Shadow Ramp Mask` Xiexie Toon will just sample the bottom row of pixels.

However, you can use the `Shadow Ramp Mask` as a greyscale texture which will select *a particular row of pixels* based on a greyscale value of 0.0 - 1.0 (or 0-255 as 8-Bit color). This means you could make a Shadow Ramp texture with **256 possible shadow ramps**!

To put it simply: Any part of your `Shadow Ramp Mask` with a greyscale value of 0.5 (127 as 8-Bit color) will use the middle row of pixels in the Shadow Ramp texture. Pure black will use the bottom row of pixels. Pure white will use the top row of pixels.

By using the `Shadow Ramp Mask` you can apply different colors/gradients of shadows to different parts of your UV map. You can also use greyscale patterns to vary and style your shadow ramp across a mesh. **This is incredibly useful for controlling the color and contrast of light on models. I strongly recomend experimenting with `Shadow Ramp Mask` to take full advantage of what Xiexie Toon can do to style your models.**

As a pratical example: If you have a model where some parts are made of metal and some parts are fabric, you can have two different shadow ramps that are selected by the `Shadow Ramp Mask`. The Shadow Ramp for the fabric can be a typical light-to-dark shadow ramp. The metal shadow ramp can take on the color of the metal and include a sharp highlight at the end of the ramp to enhance the glossy metal effect. This can be further enhanced with a HDR Shadow Ramp texture where the gloss has a value > 1.0 for a more intense shine. (Make sure the Shadow Ramp is set to a  `Prefered Profile` of Linear if you do this.)