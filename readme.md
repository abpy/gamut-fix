# Gamut fix

Gamut fix is a color transform lut to compress out-of-gamut colors into a smaller colorspace. 

### Why?:
Editing/grading color images in a large gamut colorspace produces better results, but can cause very bright or saturated colors to become out-of-gamut, or outside the range of color that can be produced on a typical display.

When converting to a smaller colorspace like sRGB, values that are too bright get clipped to 1.0 and colors that are too saturated will have negative values that are clipped to zero.
In areas of an image where one or more channels are clipped, the color becomes distorted and can appear inaccurate or as a harsh edge in what should be a smooth gradient and lack.

#### Before and After Comparison
Image processed with Adobe Standard and the Gamut Fix lut.  
color, red, green, and blue channels.

Without gamut mapping:
![without](/without%20gc.jpg)

With gamut mapping:
![with](/with%20gc.jpg)

### Who is it for?:
Anyone color-grading/editing photos or video with bright colors intended to be viewed on a screen.

### How it works.
The gamut compression algorithm is based on the one shown by Jed Smith and others on 
[GitHub](https://github.com/jedypod/gamut-compress) with a few additional steps.
1. Luminance correction after compression. 
2. The distance limit is calculated based on the edge of the larger color space.

#### Brightness compression:
Brightness is compressed using the same curve function as saturation. The amount of compression varies depending on the color. For a given color, the distance limit is calculated from the maximum possible brightness of that color in the large space relative to the small one.
For example: sRGB red can be 63% brighter in ACEScg, so the compression limit is 1.63.
The function is applied as a per channel curve.

The brightness limit can use a different color space than saturation. I used ACEScg instead of proPhoto because it is a better fit for sRGB and has a less extreme gamut.

### How to use it:
#### In Lightroom/camera raw:
If you use the Adobe Standard or Adobe Color profiles, you can use the “gamut fix” xmp profile files. The adobe color version has less brightness compression. Both support an adjustable amount from 0% to 100%.

For other camera profiles, you can use the Create Profile menu in Camera Raw.
1. Open an image with default settings (everything at 0, white balance: 'As Shot')
2. Select your camera profile
3. Option/Alt click the new preset button
4. Change the profile name
5. Load `ProPhoto to sRGB.cube` as a Color Lookup Table
6. Set Space to `ProPhoto RGB`
7. Set Amount Max to 100


#### For other editors and video:
1. The lut must be applied in the correct colorspace.
   * The  input/output colorspace is the first one in the file name and is in the cube file header.
2. It should be applied at the end of the editing process after all other filters and adjustments.
3. I made separate luts for saturation and brightness. The brightness lut can use an adjustable mix amount depending on how much is needed.

### Notes:
Some camera profiles, such as camera matching profiles, already have gamut mapping built in and do not need the extra lut.

This lut is not a replacement for pre grade gamut compression. Some subjects such as stage lighting or vividly colored flowers will need gamut compression applied before color grading. For RAW editing, this needs to be part of the camera profile.
