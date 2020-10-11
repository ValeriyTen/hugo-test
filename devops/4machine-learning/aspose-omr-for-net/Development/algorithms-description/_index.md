---
title: Algorithms Description
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-omr-for-net/Development/algorithms-description
---
### Algorithms Description
This page contains brief description of the algorithms used in Aspose.OMR for .NET library.

### Step 1. Image Preprocessing

Image is grayscale during loading. Then, image is blurred and downscaled two times (1/2 of widht and 1/2 of height) using [1 2 1] mask. Final preprocessing step is binarization. This is done using same [1 2 1] mask for computing

 small smoothed image which later used as a base for values in resulting binary image. 

### Step 2. Connected Components Search and Filtering

Connected components are found using CCL (Connected Components Labeling). Binary image is processed labeling non background pixels forming components marked by labels over image. Then this components are filtered by size since we are only interested in certain components.

Next, for each found connected component a set of features is computed, including area, bounding box, convex hull, image moments and others. This values then used to precisely filter connected components leaving only interesting ones. The filtering is done by using decision tree.

### Step 3. Template and Image Matching

When reference points are found on user image, next step is to find matching between template and image. Using image found reference points and points defined in the provided template, we calculate the matching as a perspective transform vector. Thus, we can find all bubbles positions on final image using their template position and found transform vector.

### Step 4. Recognition

Having bubbles positions, we can easily get image data for each area defining bubble from the binary image we have and then recognize the bubble. Recognition is done by calculating the amount of black pixels and based on certain threshold we decide whether the bubble is marked or not.

Then, result is packed in a tree-like structure and returned to the caller.

 