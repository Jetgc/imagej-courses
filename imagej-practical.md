

# Table of contents

[TOC]


# Author Information

<strong class="textmarkergelb"> Christian "Tischi" Tischer </strong>
tischitischer@gmail.com


# Practicals to add

## Find particles inside cells and measure their properties; identify parent cell by measuring particle intensities in label mask

## 3D objects..something with the new 3D manager


## Local Image Filtering 

- explain convolution filtering
- practical where different filters are compared: edge preserving, filter size, ...
- probably use the autophagosome data

## Automated Global Thresholding: Using the image histogram
- show different images and their histograms side-by-side
- discuss the case where there are no objects
- discuss that most algorithms assume a certain fraction of the image occupied with objects. this is a problem if this varies a lot, for example detecting nuclei (show examples)


## Automated Local Thresholding: Using local mathematics

## Smoothing improves thresholding
- add this to the autophagosome workflow by adding some noise

## Local Background Subtraction
- try subtracting a median filtered opening from a pure noise image => does the mean background value become zero?
 

# General practicals 

<div style="page-break-after: always;"></div>
## Mathematical prerequisites
- Mean and Median


<div style="page-break-after: always;"></div>
## Handling multi-color images and adding scale bar

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLQ0lWU2ZPTkF1TG8" width=200>

Aim: make the images look like the image above!

 - __[File>Import>Image Sequence] '../multi-color/'__ (*opens all images in one folder; __Mac__: just click on the folder; __Win__: click on one of the files*)
 - __[Image>Color>Make Composite] 'Display Mode = Composite'__ (*converts to an image type that is good for colors*) 
 - Use the __c__ slider at bottom of image to select a channel and then change its color via __[Image>Lookup Tables]__
 - __[Image>Properties] 'Unit of length = um' 'Pixel width = 0.16' 'Pixel height = 0.16' __ (*changes the scaling to physical distances*)
 - Add scale bar: __[Analyze>Tools>Scale Bar..] 'Overlay = Check'__
 - Save image twice: 
	 - __[File>Save As>Tiff]__
	 - __[File>Save As>Jpeg]__
 - open both images in __PowerPoint__ and compare
 - reopen both in __Fiji__ and compare

What is better for saving? Tiff or Jpeg? 

<div style="page-break-after: always;"></div>
## Tip for comparing different images

When comparing images it is a good idea to ensure that the LUT settings are the same for all images. This can be achieved with __[Image>Adjust>Brightness/Contrast..]__ clicking __[Set]__ and choosing  __‘Propagate to all other open images = Check’__.


<div style="page-break-after: always;"></div>
# Workflow: pixel- and object-based colocalisation

Literature: [A guided tour into subcellular colocalization analysis in light microscopy. S. B O LT E & F. P. C O R D E L I È R E S Journal of Microscopy, Vol. 224, 2006, pp. 213–232.](http://onlinelibrary.wiley.com/doi/10.1111/j.1365-2818.2006.01706.x/epdf)


## Segmentation: Local background subtraction and thresholding

First we segment the images, i.e. we make objects pixels 255 and background pixels 0.

- __[File>Open..] '“../colocalization/stain1.tif” __
- __[Image>Rename..] 'Title=original'__
- __[Image>Duplicate] 'Title=median'__
- __[Process>Filters>Median..] 'radius=20'__ (*3D: [Process>Filters>Median 3D..]*)
- __[Process>Image Calculator] 'Image1 = original' 'Operation = Subtract' 'Image2 = median' 'Create new window=Check'__ 
- __[Image>Adjust>Threshold..] 'lower th=30' 'upper th=255'__ (*you don't have to press [Apply] now, the actual 'applying' of the threshold will happen in the next step*)
- __[Analyze>Analyze Particles..] 'Size = 5-Infinity' 'Pixel units = Check' 'Show = Masks' [OK]__ (*selecting 'Show = Masks' generates a binary image in which only 'connected components' of at least 5 pixels are kept, i.e. 'noise' is filtered*)
- __[File>Save As..] '“../colocalization/stain1_segmented.tif” __
- You also have to do this "stain2.tif"...

## Area, object and distance-based colocalization

First we load the data and compute the overlap image:

- __[File>Open..] '“../colocalization/stain1_segmented.tif” __
- __[File>Open..] '“../colocalization/stain2_segmented.tif” __
- __[Process>Image Calculator] 'stain1_segmented.tif' 'AND' 'stain2_segmented.tif'__
- __[Image>Rename..] 'Title = overlap'__

Now, you can compute an area-based, object-based or distance-based colocalisation:


- __[Analyze>Set Measurements..] 'Centroid = Check'  'Display label = Check'__ *('Display label = check': this will associate measurements with the name of the image (and ROI) on which they were computed.)*
- For all three images, i.e. __'stain1_segmented.tif', 'stain2_segmented.tif', 'overlap'__, run the following command : 
	- __[Analyze>Analyze Particles] 'Display Results = Check' 'Summarize =Check' 'Display Labels__ *(for 3D use [Analyze>3D Objects Counter])* 
		- choose particle selection criteria that make sense for your project... 
- Object based colocalization: __Count__ (e.g., divide overlap by stain1) 
- Area based colocalization: __Area__ (e.g., divide overlap by stain1)
- Distance based colocalization: you have to write some code to find particles that are next to each other in the Results tables
 

# Workflow: Autophagosome quantification

In the following you will learn all the ingredients to perform a very typical cell biology image analysis workflow.

Your working directory is: __'../data/workflow_autophagosomes'__. This folder contains the two main input files __'autophagosomes_raw.tif'__ and __'nuclei_raw.tif'__. There are also __a lot of other files__ stored, some of which you will generate during the course. You will __overwrite__ most of those files __with your own results__!

If you messed up or missed a step there also is a sub-folder __'teacher'__, form which you can load all files that you need during the practicals :-)


<div style="page-break-after: always;"></div>
## What to measure?

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLY0RSLTBGbTFaRlk" width=300>

- __[File>Open..] 'bothChannels.jpeg'__

- Number of spots per cell
	- autophagosome initiation
	- not a 3-D image, but you could squeeze your cells...
- Size of spots
	- diffraction limit!
- Average intensity of one spot
	- diffraction limit!
	- how large should i draw my ROI?
- Sum intensity of one spot
	- autophagosome maturation
	- technical considerations:
		- microscope settings change intensities
		- total expression level changes intensities
		- good to divide by total cell intensity => fraction of protein localized to one autophagosome
		-  local background correction needed for spot intensity
- Total cell intensity
	- proportional to total amount of protein (in wide-field microscopy)
- Mean intensity of each cell
	- not clear what the biological meaning in the wide-field images is. 


<div style="page-break-after: always;"></div>
## Local background subtraction

In biological fluorescence microscopy one often wants to detect locally bright objects such as vesicular structures on top of a non-uniform background fluorescence, e.g. from unbound cytoplasmic protein. There are different methods to remove such 'background' fluorescence from the image:

- corrected\_image = image - median\_filter(image, radius) 
- corrected\_image = image - morphological\_opening(image, radius) = top\_hat(image, radius)
- corrected\_image = IJs "RollingBall" Algorithm
- *someone knowing other methods?*

In all methods the *radius* parameter should be "quite a bit larger" than the radius of the largest locally bright structure that you want to measure (why that is becomes clear when we discuss the methods in detail).

<div style="page-break-after: always;"></div>
## Local background subtraction using a median filter

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLUHVxUTVMNHVYd0E" width=700>

(Whiteboard: 1-D example)

Duplicate image and apply median filter to remove the locally bright spots. Then subtract the median filtered image from the raw image and save the result for later use. 

- __[File>Open..] 'autophagosomes_raw.tif'__
- __[Image>Rename..] 'Title=original'__
- __[Image>Duplicate] 'Title=median'__
- Select the 'median' image and __[Process>Filters>Median] 'radius=5'__
- __[Process>Image Calculator] 'Image1 = original' 'Operation = Subtract' 'Image2 = median' 'Create new window=Check' '32-bit output=Uncheck'__ 
- __[File>Save] 'spots_median.tif'__


__Exercise 1:__ Try other radii for the median filter (e.g., 1 and 100) for the median filter. What is the effect?


<div style="page-break-after: always;"></div>
## Local background subtraction using a top-hat filter 

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLb0NRUGd5aHBPVzQ" width=700>
(Whiteboard: 1-D example)

Here a morphological opening filter is applied to the image and subtracted from the original. The morphological opening is defined as the dilation of the erosion if the image. Alltogether this reads: top_hat(image) = image - dilation(erosion(image))

- __[File>Open..] 'autophagosomes_raw.tif'__
- __[Image>Rename..] 'Title=original'__
- __[Image>Duplicate] 'Title=opened'__
- __[Process>Filters>Minimum..] 'radius=5'__ 
- __[Process>Filters>Maximum..] 'radius=5'__ 
- __[Process>Image Calculator] 'original' 'Subtract' 'opened' 'Create new window=Check' '32-bit output=Uncheck'__ 
- __[File>Save] 'spots_tophat.tif'__



<div style="page-break-after: always;"></div>
## Local background subtraction using IJs "Subtract Background"

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLYkZiaTJIYlBlMkU" width=700>
(Whiteboard: 1-D example)

- __[File>Open..] 'autophagosomes_raw.tif'__
- __[Process>Subtract Background..] 'radius=5'__

This seems to implement a ['rolling ball'](https://github.com/nearlyfreeapps/Rolling-Ball-Algorithm/blob/master/rolling_ball.py) background estimation (=> Whiteboard). I don't understand the mathematical algorithm how to compute this, but based on the code that i saw it seems not so simple (see also [here](http://dsp.stackexchange.com/questions/10597/uneven-background-subtraction-rolling-ball-vs-disk-tophat) and [here](https://en.wikipedia.org/wiki/Dilation_%28morphology%29#Grayscale_dilation)).


<div style="page-break-after: always;"></div>
### Comparison of the different BG subtraction methods

Whiteboard session: 

- Difference between median-subtraction and top-hat:
	- top-hat underestimates background in presence of noise
	- median overestimates background in presence of "holes"
- Local background subtraction should only be used for (small) isolated objects
	- e.g., may fail if used to find the background intensity in an image full of cells
- using the Spheroids image in 3D_Segmentation one can d emonstrate that the top-hat (dramatically) underestimates the background in noisy images

<div style="page-break-after: always;"></div>
## Spot detection using 'Find Maxima'

(=> Whiteboard: Explanation of IJs 'Find Maxima')
ImageJ's 'Find Maxima' considers a pixel a maximum if its intensity is higher - by the 'Noise tolerance' - than neighboring pixels; see this discussion about [Topographic prominence](http://en.wikipedia.org/wiki/Topographic_prominence). 

- __[File>Open] 'spots_median.tif'__
- __Draw a line ROI__ across some of the spots and __[Analyze>Plot Profile]__
	- Check how many gray values the spots 'stand out'; use this value as 'Noise tolerance' in the next step
- __[Process>Find Maxima..] 'Noise tolerance=20' 'Output type=Single Points'__
	- check __'Preview point selection'__ and explore different 'Noise tolerance' values!
- __[Process>Math>Divide..] 'Value=255'__
	- => spot pixel will have a value of 1 (better for counting them later..)
	- The image will appear white; you have to adjust the contrast to see the dots __[Image>Adjust>Brightness/Contrast]__ 
- __[File>Save] 'spots_points.tif'__	


#### Exercise: Explore influence of local background removal   

- __[File>Open..] 'autophagosomes_raw.tif'__
- __[Process>Filters>Gaussian Blur..] 'sigma=15'__ (this removes the spots)
- __[Process>Find Maxima..] 'Noise tolerance=100'__

As you can see there are maxima detected only due to the cellular background. If you do the same using 'spots_median.tif' - where the background was removed - as input image there should be no maxima (for this 'Noise tolerance').


<div style="page-break-after: always;"></div>
## Cell detection using seeded watershed

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLUEg4TGVZQTNzOXM" width=400>

The seeded watershed algorithm implemented in ImageJ's 'Find Maxima' first finds local intensity maxima as starting ('seed') points.  From these seed points it performs a 'region growing' algorithm, using the intensity information in the image to draw dividing lines at dim parts of the image.   

- __[File>Open] 'autophagosomes_raw.tif'__
- __[Process>Filters>Gaussian Blur..] 'sigma=10'__ (*removes features that  'distract' from the overall cell shape*)
- __[Process>Find Maxima..] 'Noise tolerance=50' 'output=Segmented Particles'__ (*choosing 'Segmented Particles' invokes the Wathershed algorihm*)
- __[File>Save] 'cells_bw.tif'__

Explanation for this use-case: Look at the image after blurring it. Then imagine it starts raining. Now imagine where water running down from the different hills (bright pixels) would meet. Those are the dividing lines.

#### Exercise

Try the same leaving out the 'Gaussian Blur' step. Can you get it to work?


<div style="page-break-after: always;"></div>
## Generate cell 'objects' that can be used for measurements

We run the 'Particle Analyzer' to convert the binary cell image into 'objects' (i.e., regions of interest = ROIs). This will be handy later for cell-based measurements. 

- __[File>Open] 'cells_bw.tif'__
- __[Analyze>Analyze Particles..] 'Exclude on edges = Check' 'Add to Manager = Check'__ 
- __[File>Open] '../data_course/autophagosomes_raw.tif'__
- Click on the ROIs in the ROI Manager to see them overlayed on the raw data.

#### Exercise: Exclude cells based on their shape 

1. Explore all the different output options of the 'Particle Analyzer'!
2. Experiment with different particle exclusion criteria. For example: 

- Close 'ROI Manager' and select image: 'cells_bw.tif'
- __[Analyze>Analyze Particles..] 'Exclude on edges = Do Not Check' 'Add to Manager=Check'__ 
	- Try: '__Size = 15000-Infinity__' and '__Circularity = 0.00-1.00__'
	- Or try: '__Size = 0-Infinity__' and '__Circularity = 0.60-1.00__'
		- Click __[Help]__ to figure out what 'Circularity' is.


<div style="page-break-after: always;"></div>
## Improved cell detection by excluding background pixels

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLaFlualFWMXBKRjg" width=400>

The problem of the seeded watershed algorithm is that the 'grows into the background' (see image). To avoid this one has to threshold the cells and combine this with the results of the watershed:

- __[File>Open] 'autophagosomes_raw.tif'__
- __[Process>Filters>Gaussian Blur..] 'sigma=5'__ (*just to get rid of some noise*)
- __[Image>Adjust>Threshold..] 'lower th=230' 'upper th=Max' [Apply]__
- __[Image>Rename..] 'foreground'__ (*all background pixels are zero*)
- __[File>Open] 'cells_bw.tif'__ (*this is the image that we got from the watershed*)
- __[Process>Image Calculator..] 'foreground' 'AND' 'cells_bw.tif' [OK]__ (*we combine both...*)
- __[File>Save] 'cells_bw_improved.tif'__ (*...and get an image where we removed the background pixels and still have dividing lines between the cells*)
- __[Analyze>Analyze Particles..] 'Size=100-Infinity' 'Exclude on edges=Check' 'Add to Manager=Check'__  (*simply finds the cell objects*)
- __[ROI Manager>More>>Save..] 'cells_improved.zip'__

<div style="page-break-after: always;"></div>
## Measure spots per cell

ImageJ can measure lots of features. To have our readout more "to-the-point" we will first only select a small subset: 

- __[Analyze>Set Measurements..] 'Area = Check' 'Integrated Density = Check' 'Mean gray value = Check'__

To measure how many 'spots' (vesicular structures) we have in each cell, we simply measure in each cell ROI the integrated intensity in the 'spots_points.tif' image, where each pixel marking a spot has the value 1 (that is a very typical trick in image analysis :-).

- __[File>Open] 'spots_points.tif'__ (*you may see nothing on this image...do you know why?*)
- __[Image>Adjust>Brightness/Contrast..] [Auto]__
- __[Analyze>Tools>ROI Manager..] __
- __[ROI Manager>More>>Open..] 'cells.zip'__
- __[ROI Manager>Measure] __
- Click on Results Table and __[File>Save] 'spot_count.csv'__

The 'RawIntDen' value is the spot count. How many spots did you find?

#### Exercise
Manually draw a region on the image, add it to the ROI Manager __[ROI Manager>Add]__ and measure the number of spots in this region.

<div style="page-break-after: always;"></div>
## Global background subtraction 

If we want to measure total cell intensity in a biophysically meaningful way we have to set the image background intensity to zero.  Since cells can grow dense it can be difficult or even impossible to find the correct background value in one image. Thus, sometimes one has to manually subtract a fixed background value from the image. 

(=> Whiteboard session: why background subtraction; why convert to 32-bit.)

- __[File>Open..] 'autophagosomes_raw.tif'__
- __[Image>Adjust>Brightness/Contrast] 'Minimum=190' 'Maximum=230'__
- Measure background intensity: draw a little ROI in the background and __[Analyze>Measure..]__ 
- __[Image>Type>32-bit]__
- __[Process>Math>Subtract..] 'Value=194'__ (*Possible pitfall: if you had a ROI on the image the value was only subtracted in this ROI*) 
- __[File>Save As>Tiff..] 'autophagosomes_bgcorr.tif'__
- Measure background intensity again using __[Edit>Selection>Restore Selection..]__ to get the same ROI (*should be zero now*)

#### Exercise
Do the same but leave out the 32-bit conversion step. Now measure the intensity in the background after correction! What happens? 



<div style="page-break-after: always;"></div>
## Compute nuclear distance map
(=> Whiteboard session on Distance Transform)

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLcVpZaXZKWElYRkk" width=400>

Quite often in biology one wants to know how far a certain structure is away from another (e.g. endocytosis: vesicles from plasma membrane). Such distances often can be quite easily measured using the 'Distance Transform'.

- __[File>Open..] 'nuclei_raw.tif'__
- __[Image>Adjust>Threshold..] 'lower = 500' 'upper = Maximum' [Apply]__
- __[File>Save As>Tiff..] 'nuclei_bw.tif'__
- __[Edit>Invert]__ (*check: nuclei pixels should now be 0 and background 255; needed for Distance Map*)  
- __[Process>Binary>Options..] 'EDM output=32-bit'__ (*enables distances >255*)
- __[Process>Binary>Distance Map]__ (*pixel values are now distances to nearest nucleus*)
- __[File>Save As>Tiff..] 'nuclei_dist.tif'__

#### Exercise
...

<div style="page-break-after: always;"></div>
## Use nuclear distance map on detected spots

In order to measure the distance of each previously detected spot to the nucleus we (almost) simply multiply the distance map with the spot image. The only problem we have is that a zero in the final image could mean: (i) there was no spot or (ii) there was a spot but its distance to the nucleus was zero. To distinguish these cases we will  set non-spot pixels to NaN (Not a Number) before we do the multiplication.    

<img src="http://drive.google.com/uc?export=view&id=0B3D4N0YB6omLcXAwcnItZHZiUVk" width=400>

- __[File>Open..] 'spots_points.tif'__ (*pixel values: 1 = spot; 0 = no spot*)
- __[Image>Type>32-bit]__ (*necessary to enable NaN values*)
- __[Image>Adjust>Threshold..] 'lower=0.5' 'upper=1' [Apply]__ When asked: __Check 'Set background pixels to NaN'__

- __[File>Open..] 'nuclei_dist.tif'__ (*pixel values: distances to nearest nucleus*)
- __[Process>Image Calculator..] 'Image1 = nuclei_dist.tif' 'Operation = Multiply' 'Image2 = spots_points.tif' '32-bit result = Check'__ 
	- *the value of each pixel is now distance of the spot to nearest nucleus or NaN if there was no spot*
- __[File>Save As>Tiff..] 'spots_dist2nuc.tif'__


<div style="page-break-after: always;"></div>
## Measure intensity inside autophagosomes
(=> Whiteboard session on intensity measurements in diffraction limited objects in the presence of local background (unbound protein))

Often one wants quantify the intensity of objects as it reports the amount of bound labelled protein. Here, we use the 'spots_median.tif' image, where the cytoplasmic background has already been subtracted. In order to restrict the intensity measurement to the region of the spots, we use the 'spots_point.tif' image, where the center of each spot has the value 1 and the other pixels are 0. In order to measure the whole spot intensity we will dilate this image and then multiply (mask) onto the 'spots_median.tif' image (for the masking we need to set pixels outside spots to NaN (Not a Number)).

- __[File>Open..] 'spots_points.tif'__ (*pixel values: 1 = spot; 0 = no spot*)
- __[Process>Filters>Maximum..] 'radius=2'__   (*enlarge spots to include all fluorescence*)
- __[File>Open..] 'spots_median.tif'__ (*pixel values: background corrected autophagosome intensities*)
- __[Process>Image Calculator..] 'Image1 = spots_points.tif' 'Operation = Multiply' 'Image2 = spots_median.tif' '32-bit result = Check'__ 
	- *pixel values: inside spots: background corrected spot intensity; outside spots: NaN*
- __[File>Save As>Tiff..] 'spots_intensity.tif'__

<div style="page-break-after: always;"></div>
## Perform all kinds of cell based measurements 

Once we have the cell ROIs we can measure many cell-based features, simply loading different input images for the measurement: 

- __[Analyze>Tools>ROI Manager..]__ 
- __[ROI Manager>More>>Open..] 'cells.zip'__ or 'cells_improved.zip'

- __[File>Open] 'spots\_points.tif', 'autophagosomes\_bgcorr.tif', 'spots\_intensity.tif', and 'spots\_dist2nuc.tif'__ (*if you did not generate all these images yourself you can load them from the 'teacher' folder*) 
-  For each of above input images: 
	- Select the input image (i.e., click on it once)
	- __[ROI Manager>Measure]__ 
	- Click on the Results Table and __[File>Save] '*a good name*.csv'__
	- Close Results Table! (*otherwise the next measurement values will be appended in case you want to measure another image*)

#### Exercise
Try to remember the biological interpretation of these measurements. Think about ratios of any of the measured numbers.


<div style="page-break-after: always;"></div>
## Automation using the IJ macro language 

In order to apply the previous measurements to more than one image, we will now automate all the step that we did previously. For this we will use the ImageJ Macro language (http://rsb.info.nih.gov/ij/developer/macro/macros.html) and ImageJ's inbuilt  Macro Recorder [Plugins>Macros>Record].

<div style="page-break-after: always;"></div>
### Create a macro for automated cell detection

First clean up things, e.g. by restarting ImageJ, and then turn on the Macro Recorder [Plugins>Macros>Record..]

Execute below commands for automated cell detection (these are the same commands we used earlier):

- [File>Open] 'autophagosomes_raw.tif'
- [Process>Filters>Gaussian Blur..] 'sigma=10'
- [Process>Find Maxima..] 'Noise tolerance=100' 'Output type=Segmented Particles'
- [Analyze>Analyze Particles..] 'Size=100-Infinity' 'Exclude on edges=Check' 'Add to Manager=Check' 
- In the Macro Recorder click [Create].
	- The 'Script Editor' with your macro will appear. 
- Close all windows (also the ROI Manager).
- In the Script Editor click [Run].
	- Did it work? If yes: great! Maybe your first code! 
- Script Editor: [File>Save As..] '.../CellDetection.ijm'

#### Advanced task

Look at the macro for the improved cell detection with exclusion of background pixels:
- [File>Open..] '.../teacher/CellDetection2.ijm'
Try to understand the code. Look up commands that you don't know on
http://rsb.info.nih.gov/ij/developer/macro/functions.html

<div style="page-break-after: always;"></div>
### Create a macro for automated spot detection

First clean up things, e.g. by simply restarting ImageJ, and turn on the Macro Recorder [Plugins>Macros>Record..]

Execute below commands for automated spot detection (same commands we used earlier):

- [File>Open] 'autophagosomes_raw.tif'
- [Image>Rename..] 'Title=raw'
- [Image>Duplicate] 'Title=median_bg'
- Select 'median_bg' image and [Process>Filters>Median] 'radius=5'
- [Process>Image Calculator] 'raw' 'Subtract' 'median_bg' 'Create new window=Check' '32-bit output=Uncheck' 
- [Process>Find Maxima..] 'Noise tolerance=100' 'Output type=Single Points'
- [Process>Math>Divide..] 'Value=255'
- [Image>Rename..] 'spots_points.tif'

- Script Editor: [File>Save As..] '.../SpotDetection.ijm'
- Script Editor: [Run]



<div style="page-break-after: always;"></div>
### Combine cell- and spot-detection macros and add the spot-per-cell measurement

Open both macros (of course, if you have them both open already you can skip this step) and then simply code and paste the SpotDetection code below the CellDetection code.

- [File>Open] 'CellDetection.ijm' (or 'CellDetection2.ijm')
- [File>Open] 'SpotDetection.ijm'
- Script Editor: Copy code of SpotDetection below CellDetection
- Script Editor: [File>Save] 'CellAndSpotDetection.ijm'
- Script Editor: [Run] 

Now we need to add the spot-per-cell counting by recording the appropriate macro commands:

- Close the Macro Recorder and restart it: [Plugins>Macros>Record..]
- Click on the 'spots_points.tif' image
- Roi Manager: [Measure]
- Results table: [File>Save As..] 'spot_count.csv'
- Macro Recorder: [Create]
- Script Editor: Copy your new code at end of 'CellAndSpotDetection.ijm' 
- Script Editor: [File>Save] 'CellAndSpotDetection.ijm'
- Script Editor: [Run]

#### Exercise 

Run the code twice in a row without closing any windows. Confirm that both the content of the ROI Manager and the content of the Results table get messed up. What happens?

<div style="page-break-after: always;"></div>
### Close all windows in a macro (help yourself!)

We saw above that we need to automatically close Results Table and ROI Manager before running our macro. Also it would be good to close all image windows. In this case macro recording not always helps (at least on the teachers Mac for a specific version of ImageJ and Fiji). Try to find the right commands using any of the following approaches:

- Have some images, Results Table and ROI Manager open (e.g. run CellAndSpotDetection.ijm) and record yourself closing them all [Plugins>Macros>Record..]; [Create] the macro and try if it works!
- Google: 'imagej macro close results table'
- Google: 'imagej macro close roi manager'
- Google: 'imagej macro close all images'

What did you find?

If you found nothing useful you can see what I found:

- [File>Open..] '../teacher/closeEverything.ijm'

<div style="page-break-after: always;"></div>
## Adding a function to close all windows
Above we already found the commands to close all windows. Here we will see how to neatly pack them into a "function" such they can be called conveniently from every point of your macro.

- [File>Open..] '../teacher/CellAndSpotDetection_Improved.ijm'
- Script Editor: [Run]
	- run it again: as you see now it works even twice!

=> Discussion of the code with help of teacher.

<div style="page-break-after: always;"></div>
## Adding code to select an arbitrary image

Right now our 'CellAndSpotDetection_Improved.ijm' only runs on one specific image. Let's look at a slightly modified version that makes it useable for any given input image:

- [File>Open..] '../teacher/CellAndSpotDetection_ChooseImage.ijm'
	- do you understand the code?
- [Run] macro on files in '../data_small/'
- [Run] macro on files in '../data_large/'

=> Discussion of the code with help of teacher.

<div style="page-break-after: always;"></div>
## Batch me if you can

Of course, we'd like to run a macro on many images. In ImageJ there is the function [Process>Batch>Macro..], which automatically applies a macro to all files in the 'Input..' folder. However, this function is very limited, for instance it cannot handle saving results tables.    

As a more flexible alternative here is some code that handles the batch processing and you (more-or-less) only have to copy and paste your specific macro into it:

- [File>Open..] '../teacher/runAsBatch.ijm'

This code involves some 'real programming' that, in principle, you don't have to understand to use it. We will anyway discuss it together just to give you an idea.

(=> Discussion of the code with teacher.)

<div style="page-break-after: always;"></div>
## Batch cell and spot detection

Ok, let's have a look the final code, which apart from a few marked changes is just the central part of 'CellAndSpotDetection_ChooseImage.ijm' copied into 'runAsBatch.ijm':

- [File>Open..] '../teacher/CellAndSpotDetection_Batch.ijm'
- [Run] on 'data_small'
- [Run] on 'data_large'
- Examine the output tables in Excel


#### Exercise

Think about the following: Is this code ready to use for a project? What is missing? 


<div style="page-break-after: always;"></div>
## Batch processing considerations  

The macro 'CellAndSpotDetection_Batch.ijm' is not bad but it is also missing a few things:

- possibly: add more measurements; they could be simply added by recording and copying more code into the current script.
- essentially: saving of output images for visual quality control!
	- e.g., raw data with overlay of cell boundaries
	- e.g., raw data with overlay of detected spots
- possibly: add graphical user interface for some of the parameters:
	- e.g., minimal cell size
	- e.g., 'noise tolerance' for spot detection
	- e.g., threshold that distinguishes background from cells
- possibly: add more cell filter criteria:
	- e.g., remove cells from analysis that do not express enough protein

It is possible to add all this in ImageJ however I highly recommend to also check the free [CellProfiler](http://cellprofiler.org) software. CellProfiler enables you to perform these operations without actual programming. Note that as of June 2015 CellProfiler only works for 2-D data but this may change.