#  __`SurfaceDefects`__ Python Package
A simple but powerful Python package capable of
 1. `detecting pores` in concrete, steel and similar surafaces, or the `fabrication errors` for that matter. 
 2. determining the `area` of each pore.
 3. calculating `equivalent diameter` of each pore. Equivalent diameter is the diameter of a circle whose area equals that of a pore. More on it later...
 4. calculating `surface porosity` i.e., the ratio of area covered by pores to the total area.
 5. plotting `circles` over pores once the `equivalent diameters` are determined.
 6. assigning length `scale` to image. 


   It works on the spirit of `Computer Vision` and segmentation.  The Python package `SurfaceDefects` classifies surfaces based on the difference in their pixel values. It can also generally be used for detecting contours in 2D images of any type provided they are suitable for the intended purpose. 

>## Better images lead to better results!


The concept will develop as we go down the documentation.

## 1. Install `SurfaceDefects`
```
~ pip install SurfaceDefects
```
## 2. Import into the project alongwith `matplotlib.pyplot`
```
import SurfaceDefects as sdf
import matplotlib.pyplot as plt
```
## 3. Load image using `LoadImage()` function 
```
img = sdf.LoadImage(path, gray=Fasle) 
```
`path`: is the complete path to your image

`gray`: whether to load the image as a grayscale image, default is `False`

>## Tip: Load `img` as it is and another `img_gray` as a graysacle image. You will need both of them later. 

## 4. Make a `black` replica of `img` (not compulsory)
```
black = sdf.PseudoImage(img) 
```
## 5. Detect pores/defects using `ClassifySurface()` function
```
thresh = sdf.ClassifySurface(img_gray, threshold, invert_color=True)
```
```
plt.imshow(thresh, cmap='gray')
```
`thresh`: the resultant image is better known as _thresholded_ image hence `thresh`

`img_gray`: the original _grayscale_ image 

`threshold`: verily the most important parameter, `threshold` is what that controls the detection of pores/defects. Ranges between 0 (black) and 255 (white). 

`invert_color`: swaps colors i.e., black to white. Default is `True`
___
>plt's `cmap='gray'` for a grayscale image.


![threshold_plots](https://user-images.githubusercontent.com/120237699/207210495-4e621a96-b44f-4b52-9a77-660f03a21304.png)

## 6. Find boundary of pores/defects using `FindContours()` function
>Contours are lines joining points of same instensity. 

Those points in this case are located along the boundaries of pores. 
```
contours, _ = sdf.FindContours(classified_img)
```
`contours`: an `array` 

`_`: the `hierarchy` and can be ignored in this particular case.

`classified_img` = `thresh`, the _thresholded_ image.

## 7. `SignificatContours()` 
Some of the contours found by `FindContours()` might be insignificant in that their areas might be _zero_. `SignificatContours()` will automatically discard contours whose areas are _zero_.
```
sig_contours, _ = sdf.SignificantContours(classified_img)
```
`sig_contours`: an `array` 

`_`: the `hierarchy` and can be ignored in this particular case.

`classified_img` = `thresh`, the _thresholded_ image.

## 8. Define a scale using `DefineScale()` function
```
scale_factor = sdf.Scale(height, width) 
```
`scale_factor`: a ratio of scale, or better known as a _Referesentative Factor_

`height`, `width`: height and width of real object/surface in units of length 

## 9. Calculate area of pores using `ContourArea()`
```
pore_area = sdf.ContourArea(classified_img, sum=False, scale_factor=1)
```
If  `sum`=`True`, total pore area will be calculated; otherwise a `list`. 

`scale_factor`: a ratio of scale, or better known as a _Referesentative Factor_. Default value is $1$ which means no conversion from units of pixel to units of length.

## 10.  `EquivalentDiameter()` calculates diameter of a circle whose area equals that of a pore

```
equ_dia =  sdf.EquivalentDiameter(contours, scale_factor=1)
```
## 11. `DrawContours()` draws boundaries of each pore
```
contour_img = sdf.DrawContours(img,contours, index=-1, color = (0,255,0), thickness=1)
```
```
#display
plt.imshow(contour_img)
```
`img`: original or `black` img

`contours`: `array` from `DrawContours()` or `SignificantContours()`

`index`: which contour boundary to plot? $-1$ means all. Similarly $2, 3, 4$ for number $3$<sup>rd</sup>, $4$<sup>th</sup>, $5$<sup>th</sup> respectively, and so on.

`color`: color of boundary line, default is green.

`thickness`: thickness of boundary line, default is 1.

## 12. `DrawCircles()` draws circle aroud the boundary of `equivalent diameter`

```
circle_img = sdf.DrawCircles(img, contours, color=(0,255,0), thickness=1)
```
```
plt.imshow(circle_img)
```
Refer to point number $11$ for parameters/arguments.

![contours vs circles](https://user-images.githubusercontent.com/120237699/207210503-17d495b6-710b-4537-aa94-44e030a0a4ad.png)


## 13. `Denoise()` smoothens/denoises the original image
```
#applies GaussianBlur filter of opencv-python

denoised_img = sdf.Denoise(img, kernel=(3,3), channels=0)
```

`kernel`: default ($3,3$)

`channels`: $3$ for an RGB/BGR image and $0$ for a grayscale image

## 14. `SurfacePorosity()` calculates the ratio in percentage of area covered by pores to the total area

```
porosity = sdf.SurfacePorosity(img, contour_area)
```
`contour_area`: a float or an integer

_______________________

# Acknowledgement | Dependencies
1. `opencv-python`
2. `numpy`
