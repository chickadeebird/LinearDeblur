# LinearDeblur

This is a Pixinsight script for implementing different machine learning and analytic linear deblurring algorithms. These are mostly used to repair the appearance of stars when guiding has been suboptimal.

The different scripts are implemented in the tabs of the main dialog box. They work on both mono and RGB nonlinear images.

# Algorithm 1 - Rotate and Deblur with Background

This part of the script uses a machine learning based model to mimic a linear debluring deconvolution and is implemented on both the stars and the background. This algorithm is implemented to avoid deblurring any non-circular galaxies that are found in the field of view.

The algorithm steps are:

1. Crop the middle of the image to isolate a sample of the central stars. Any stars that have artifacts due to edge effects are therefore not considered.
2. Use the Pixinsight StarDetector to find all of the stars in this cropped middle portion.
3. Use DynamicPSF to characterize all of the stars that have been detected. This will find the rotated x and y axes of all of the stars as well as their rotation angles.
4. Find the median rotated angle (theta) for all of the stars. This median rotated theta should then characterize the angle at which the stars have been blurred.
5. Rotate the image by the median rotated theta so that the stars are oriented along the horizontal plane.
6. Call a machine learning executable to deblur both the full image stars and the background along the horizontal plane only, avoiding any distortion to any out of plane structures (such as galaxies or drones over New Jersey).
7. Rotate the back to the original orientation.
8. Crop the image to the original image dimensions.

They will work on both mono and RGB images at present.

Theoretically, this algorithm is technically superior to the "stars only" approach below because the linear blurring effect on the image (from guiding problems for example) would occur on both the stars and the background. However, the result may or may not be as visually pleasing as the "stars only" approach.

# Algorithm 2 - Rotate and Deblur Stars Only

This part of the script uses a machine learning based model to mimic a linear debluring deconvolution and is implemented on the stars only, sparing the background from any modifications and hopefully avoiding the introduction of any artifacts into the background.

# Algorithm 3 - Generate Synthetic Stars

This part of the script creates a starfield of sythetic stars matching the stars within the image.

The algorithm steps are:

1. Use the Pixinsight StarDetector to find all of the stars in this cropped middle portion.
2. Use DynamicPSF to characterize all of the stars that have been detected. This will find the rotated x and y axes of all of the stars as well as their rotation angles.
3. Create a new synthetic star based on the Moffat function that has the same star location but the x and y axes are now artifically made to both be the smaller of the two, thus creating rounded stars.

This will create a separate image of stars that can be used to replace any stars that have been removed using other tools such as StarNet2. This will likely remove any small galaxies not detected by the Pixinsight star detector.

# Algorithm 4 - Denoise (an out of context maching learning denoising algorithm)

This is a denoising algorithm for nonlinear images. This is a test of the deblurring model to see if it can also act as a denoising model. The denoising seems to work relatively well and may find itself as part of a group of denoising models that I have implemented. This is included in this script only because the machine learning executable is the same as for the deblurring algorithms above, with the addition of the training weights (which is a relatively small file).

The following image pair is a result of the denoising algorithm on 18x5min Luminance of the Gamma Cassiopeia region.

<img src="./figs/Denoising Gamma Cas.png" text='Denoised image using the Denoise script - left noisy stack of 18x5min Luminance, right denoised' align=left />

This next image pair is a result of the denoising algorithm on a single Luminance frame (5min) of the Gamma Cassiopeia region. The only processing done on the single 5min image frame is a Dynamic Background Extraction and it was saved as a float as the current implementation only denoises float images - update to follow for integer.

No further histogram manipulations were done after the denoising - however, the decreased noise permits further stretching and subsequent denoising passes.

<img src="./figs/Denoising Gamma Cas single.png" text='Denoised image using the Denoise script - left noisy stack of 18x5min Luminance, right denoised' align=left />

## Uses

The script works on stars that are linearly blurred, oblong, egg-shaped, etc.

It currently is only implemented on the Windows platform, but I would be pleased to implement it on other platforms if someone is willing to help me. I have no access to Linux- or Mac-based machines at present.

## Images

This is a sample pair of a 5 minute Luminance image with blurring (left), and its repaired/deblurred image (right).

<img src="./figs/Deblurred Stars Only.png" text='Deblurred stars using the "Stars Only" script - left blurry stars, right deblurred' align=left />

This result is from using the "Stars Only" tab.

## Script

This is the script interface for the LinearDeblur script.

<img src="./figs/LinearDeblur Script.png" text='LinearDeblur script' align=left />

This can be found here: ChickadeeScripts > LinearDeblur

## Manage repository location

In order to automatically install and subsequently refresh script updates in Pixinsight, add the following URL to Resources > Updates > Manage repositories

https://raw.githubusercontent.com/chickadeebird/LinearDeblur/main/

After this has been added to the repositories, Check for updates should place the new LineRepair and BatchLineRepair scripts in Scripts > ChickadeeScripts

## Machine learning files location

The Linear Deblur script calls an executable file that is a machine learning program trained for the deblurring and denoising functions for the script. The executable can be downloaded via the following link:

https://drive.google.com/file/d/1MjXowmiCtcMl129XYs_Du68cEyjhegv3/view?usp=sharing

Place the zip file in a location on your computer and unzip the contents. Start the LinearDeblur script in Pixinsight under the Scripts > ChickadeeScripts location. Click on the wrench at the bottom left of the LinearDeblur dialog box and then move to the location where the executable files have been unzippped. The enables the LinearDeblur script to find the executable in the correct location.
