# VR_Assignment1


VR_Assignment1_SaiShrutiPrakhya_MS2024018/
├── src/
│   ├── coin_detection_and_segmentation.py
│   └── panorama.py
├── Input_Images/
│   ├── Indian_Coins.png
│   ├── Hostel_Common_Room_1.JPG
│   ├── Hostel_Common_Room_2.JPG
│   ├── Hostel_Common_Room_3.JPG
│   ├── Outdoor_1.JPG
│   ├── Outdoor_2.JPG
│   ├── Outdoor_3.JPG
│   ├── Pan_Living_Room1.jpg
│   ├── Pan_Living_Room2.jpg
│   └── Pan_Living_Room3.jpg
├── Results/
│   ├── Closed_Edges_Coins_Suboptimal.png
│   ├── Detected_Coins_Optimal.png
│   ├── Detected_Coins_Suboptimal.png
│   ├── Detected_Coins_Suboptimal2.png
│   ├── Detected_Coins_Suboptimal_1.png
│   ├── Edges_Coins_Suboptimal.png
│   ├── Indian_Coins_Optimal.png
│   ├── Indian_Coins_Suboptimal.png
│   ├── Segmented_Coins_Optimal.png
│   ├── Segmented_Coins_Suboptimal.png
│   ├── Segmented_Coins_Suboptimal2.png
│   ├── Hostel_Common_Room_Keypoints.png
│   ├── Outdoor_Keypoints.png
│   ├── Outdoor_Panorama.png
│   ├── Pan_Hostel_Common_Room.png
│   ├── Pan_Living_Room_Final.png
│   ├── Pan_Living_Room_Keypoints.png
│   └── Pan_Living_Room_distorted.png
├── README.md
└── requirements.txt
# Question 1: Coin Detection, Segmentation and Counting

## Optimal Implementation

Initial parameters were too restrictive, missing several unique coins:
- A hexagonal coin
- A highly textured coin
- A ring-shaped coin

### Parameter Optimization Process

1. **Improvement (32 coins)**
   - Reduced min_area to detect smaller coins
   - Lowered circularity threshold to accommodate hexagonal shape
   - Modified parameters:
     ```python
     min_areas = [150, 175, 200]
     circularities = [0.45, 0.5, 0.55]
     min_radii = [25, 27, 30]
     ```
     Please scroll down to check the iterations made to get to 32 coins.

2. **Final Optimization (33 coins)**
   - Further reduced min_area to 75
   - Fine-tuned circularity to 0.44
   - Final parameters:
     ```python
     min_area = 75
     canny_low = 30
     canny_high = 100
     circularity = 0.44
     min_radius = 30
     ```

### Challenges Encountered

The highly textured coin presented a unique challenge:
- Its complex surface pattern created multiple edge detections
- Lower circularity threshold (0.44) was required to detect it
- This adjustment resulted in losing detection of another coin with cleaner edges
- Trade-off between detecting textured coins vs maintaining accuracy for regular coins

### Grid Search Improvements
1. Used more granular parameter steps
2. Extended parameter ranges to accommodate edge cases
3. Focused on critical thresholds where coins were being gained/lost
4. Balanced parameters to maximize total coin detection while minimizing false positives

The results are depicted below:

![Final1](Results/Indian_Coins_Optimal.png)
![Final2](Results/Detected_Coins_Optimal.png)
![Final3](Results/Segmented_Coins_Optimal.png)





## First Implementation

• Initial Implementation Issues:

1. Merged Coin Detection:
   - Adjacent coins incorrectly detected as single objects
   - Edge detection and morphological operations creating unwanted bridges
   - Particularly problematic for touching or closely placed coins

2. Missed Detections:
   - Failed to detect all coins due to:
     * Strict Canny thresholds (200-250)
     * Poor blur kernel parameters
     * Ineffective morphological operations
   - No shape validation or distance-based separation
   - Resulted in significant under-counting

3. Loss of Interior Details:
   - Binary masking (cv2.bitwise_and) eliminated internal features
   - Inconsistent preservation of details due to lighting variations
   - Stronger contrast coins retained texture while others reduced to outlines
  ![Segmented Coins](Results/Segmented_Coins_Suboptimal.png)
  ![Detected Coins](Results/Detected_Coins_Suboptimal.png)

  ## Second Implementation
  • Improvements:
  - Optimized Canny edge detection (100-200 vs 200-250)
  - Enhanced contrast using CLAHE
  - Added circularity checks and distance-based separation
  - Better segmentation with preserved internal details
  - Reduced merged coin detection

  • Current Issue:
  - Under-detection (22 vs 34 coins) due to strict filtering parameters (area, circularity, distance) that exclude valid coins.

  ![Detected Coins2](Results/Detected_Coins_Suboptimal_1.png)

  ## Third Implementation

![Final1](Results/Indian_Coins_Suboptimal.png)
![Final2](Results/Detected_Coins_Suboptimal2.png)
![Final3](Results/Segmented_Coins_Suboptimal2.png)

### Coin Detection Algorithm Improvements
#### Grid Search Optimization

The algorithm successfully detected 30/34 coins through systematic parameter optimization.
1. Core Search Parameters

    Minimum Areas: [200, 300, 400]

    Canny Low Thresholds: [30, 40, 50]

    Canny High Thresholds: [100, 120, 150]

    Circularity Thresholds: [0.7, 0.75]

    Minimum Radius: [25]

2. Key Improvements
Preprocessing:

    - CLAHE enhancement (clipLimit=2.0, tileGridSize=(8,8))

    - Gaussian blur with (7,7) kernel

    - Two-stage Canny edge detection

Morphological Operations:

    - Small kernel (3,3) for precise edge operations

    - Single iteration dilation

    - Sequential dilate → erode → close pipeline

Contour Processing:

    - Dynamic area filtering (200-50000 pixels)

    - Strict circularity check (0.75)

    - Minimum radius validation (25 pixels)

    - Close coin detection (10 pixel threshold)

Segmentation Optimization:

    - Two-pass contour filtering

    - Center-based position tracking

    - Adaptive parameter selection

Results:

    - Detection Accuracy: 88% (30/34 coins)

    - Robust edge preservation

    - Minimal false positives

  4 coins missed due to:
  - Low contrast with background (4th from left in top row)
  - Different texture from others (2 coins in the bottom row)
  - Intensity variations (middle row)

  
  

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
   - I tried to perform stitching on another indoor scene - hostel common room and an outdoor scene - consisting of lots of trees (balcony view). The results are summarised below.
   - There was a huge variation in panorama quality between the two indoor scenes - living room(Refer to the Optimal Results section) and the hostel common room (elaborated below).
   -  The living room scene had more favorable characteristics for stitching: consistent depth of field, well-distributed features across the frame, and uniform lighting conditions.
   -  In contrast, the hostel room scene presented more challenging conditions. The presence of a sofa in close proximity to the camera created significant parallax error.
   -  This parallax effect violates the fundamental assumption in panoramic stitching that the scene can be approximated as a planar surface viewed from different angles.
   -  While the transformation to LAB color space and subsequent image enhancement operations (smoothing and edge enhancement) helped improve feature detection by reducing noise and emphasizing structural elements, they couldn't fully compensate for the inherent geometric challenges posed by the varying depth planes.
   -  The LAB color space transformation was particularly helpful because it separated luminance from chrominance, allowing for better feature detection in areas with varying lighting conditions, but the fundamental issue of parallax distortion from the close-up sofa remained a limiting factor in achieving seamless stitching.
   -  Note that in the results below "Original Image" refers to the RGB image itself and the "Enhanced Image" refered to the image in the LAB color space.
   -  Additionally, the presence of windows in the hostel room scene introduced significant exposure variations, with bright light sources creating high contrast areas and uneven illumination across the capturing plane. These extreme lighting differences from the windows caused inconsistent feature detection between overlapping images and created challenges in blending areas where bright window light met darker indoor shadows, further complicating the stitching process.
   ![Hostel Common Room Keypoints](Results/Hostel_Common_Room_Keypoints.png)
   ![Hostel Common Room Panorama](Results/Pan_Hostel_Common_Room.png)
   - The panorama stitching of the outdoor scene presented interesting challenges and results.
   -  The algorithm successfully maintained color consistency and achieved decent alignment in the central regions, particularly in the tree foliage and ground areas, it struggled with perspective distortion at the edges.
   -  The presence of approximately 100,000 keypoints per image, while providing extensive feature detection, may have introduced noise in the matching process.
   -   Notable issues include black regions at the image boundaries and visible seam lines, particularly in the walkway area.
   -   These artifacts can be attributed to parallax errors from varying object depths, the repetitive patterns in the foliage challenging the feature matching process, and possible camera rotation during capture.
   -   The results as shown below highlight the delicate balance needed between having sufficient keypoints for matching while avoiding noise that can affect the homography calculation and final stitching quality.
   ![Outdoor Keypoints](Results/Outdoor_Keypoints.png)
   ![Outdoor Panorama](Results/Outdoor_Panorama.png)


