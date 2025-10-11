# Custom Homography Computation with RANSAC

## Overview

This implementation adds custom homography computation using the RANSAC algorithm to `Assignment.ipynb`. The implementation is located in cells 24-31 of the notebook.

## What Was Implemented

### 1. Direct Linear Transform (DLT) Algorithm
- **Function**: `compute_homography_dlt(src_points, dst_points)`
- Computes the 3x3 homography matrix from point correspondences
- Uses Singular Value Decomposition (SVD) to solve the homogeneous system
- Normalizes the result so that H[2,2] = 1

### 2. RANSAC Algorithm
- **Function**: `ransac_homography(src_points, dst_points, num_iterations, threshold)`
- Robust estimation of homography in the presence of outliers
- Randomly samples 4 point correspondences
- Iteratively finds the best model based on inlier count
- Refines the homography using all inliers

### 3. Feature Detection and Matching
- Uses SIFT (Scale-Invariant Feature Transform) for keypoint detection
- FLANN-based matcher for efficient feature matching
- Lowe's ratio test (threshold=0.7) to filter good matches

### 4. Comparison with Ground Truth
- Loads ground truth homography from the graf dataset (H1to2p file)
- Computes multiple comparison metrics:
  - Element-wise absolute differences
  - Frobenius norm of the difference matrix
  - Reprojection errors for both homographies
- Visual comparison through image warping

## Results

When tested on the graf dataset (img1.ppm → img2.ppm):

- **Inlier Ratio**: ~95.85% (992/1035 matches)
- **Frobenius Norm Difference**: ~0.73
- **Mean Reprojection Error**:
  - Custom RANSAC: ~5.00 pixels
  - Ground Truth: ~4.99 pixels

The custom implementation achieves results very close to the ground truth homography, demonstrating the effectiveness of the RANSAC algorithm.

## How to Use

1. Open `Assignment.ipynb` in Jupyter Notebook
2. Navigate to cells 24-31
3. Execute the cells in sequence:
   - Cell 24: Read the documentation
   - Cell 25: Define DLT and reprojection error functions
   - Cell 26: Define RANSAC function
   - Cell 27: Extract and match features
   - Cell 28: Compute custom homography
   - Cell 29: Load ground truth
   - Cell 30: Compare metrics
   - Cell 31: Visual comparison

## Dependencies

- `opencv-python` (cv2)
- `numpy`
- `matplotlib`
- `scipy`

## Technical Details

### DLT Algorithm
The DLT algorithm solves for the homography matrix H such that:
```
dst_point = H × src_point
```

For each point correspondence (x,y) → (u,v), we create two equations:
```
-x  -y  -1   0   0   0  ux  uy  u
 0   0   0  -x  -y  -1  vx  vy  v
```

The homography is the eigenvector corresponding to the smallest eigenvalue.

### RANSAC
The algorithm follows these steps:
1. Randomly select 4 point correspondences
2. Compute homography H using DLT
3. Count inliers (points with reprojection error < threshold)
4. Keep the best H (with most inliers)
5. Refine H using all inliers

Typical parameters:
- Number of iterations: 2000
- Inlier threshold: 3.0 pixels

## Comparison Metrics

1. **Frobenius Norm**: ||H_custom - H_groundtruth||_F
2. **Element-wise Differences**: |H_custom[i,j] - H_groundtruth[i,j]|
3. **Reprojection Error**: √((projected_x - actual_x)² + (projected_y - actual_y)²)

## References

- Hartley, R., & Zisserman, A. (2003). Multiple View Geometry in Computer Vision
- Fischler, M. A., & Bolles, R. C. (1981). Random sample consensus: a paradigm for model fitting
- Lowe, D. G. (2004). Distinctive image features from scale-invariant keypoints
