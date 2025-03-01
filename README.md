# VR_Assignment1

# Question 2: Panorama Stitching

Keypoints of 3 overlapping images were identified using SIFT and they were stitched together using OpenCV's stitcher class. The following section shows the final satisfactory output. The next section will show the outputs that were not satisfactory and then the analysis of the entire process. 

## Optimal Results
### Input Images

The following three images of a living room were used as input:

![Input Image 1](Input_Images/Pan_Living_Room1.jpg)
![Input Image 2](Input_Images/Pan_Living_Room2.jpg)
![Input Image 3](Input_Images/Pan_Living_Room3.jpg)

### Process

1. **Image Loading**: 
   - Three overlapping images were loaded using OpenCV
   - Images were converted from BGR to RGB color space for visualization

2. **Feature Detection**:
   - SIFT (Scale-Invariant Feature Transform) algorithm was used for feature detection
   - SIFT parameters were tuned with contrastThreshold=0.04 for better feature detection
   - Keypoints were detected and visualized on each image:

![Keypoints](Results/Pan_Living_Room_Keypoints.png)

3. **Image Stitching**:
   - OpenCV's Stitcher class was used in PANORAMA mode
   - Images were automatically aligned and blended
   - The final panorama was generated:

![Final Panorama](Results/Pan_Living_Room_Final.png)

### Implementation

The implementation uses the following key components:
- OpenCV for image processing and stitching
- SIFT algorithm for feature detection
- Matplotlib for visualization
- NumPy for array operations

## Alternative Attempts and Analysis

1. **Using a custom stitching function for creating the panorama**
   - The distortion in the panorama can be attributed to several factors in this implementation.
   - The FLANN-based matching with a ratio test threshold of 0.8 could be too lenient, potentially including incorrect matches that affect the homography calculation.
   - The homography estimation using RANSAC with a reprojection threshold of 5.0 might not be optimal for these specific images, causing perspective distortions.
   - The blending approach using a Gaussian mask, while attempting to create smooth transitions, might have introduced ghosting effects when the alignment wasn't perfect.
   - The sequential stitching approach (stitching images one by one) can accumulate errors, as any misalignment in earlier steps propagates and amplifies through subsequent stitching operations. This explains why the final panorama appears distorted compared to the OpenCV Stitcher class implementation, which uses a more sophisticated global alignment and blending strategy.
![Distorted Panorama](Results/Pan_Living_Room_distorted.png)

2. **Using other sets of images**
   - 

