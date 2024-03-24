## bm3d

# Demo examples on the use of the new bm3d filtering program (in LAT module). 
# Besides backscatter images complex valued images as interferograms or rasterfiles can be filtered.

# bm3d - Block-Matching and 3D filtering for reducing noise in images.

## EXAMPLE
# bm3d 20110216.mli 3395 20110216_bm3d.mli 0 0 25

# Reduces noise in 20110216.mli using the SAR-BM3D method.

## DESCRIPTION
# Block-Matching and 3D filtering [1] is an advanced non-local algorithm for reducing noise in images. The implemented algorithm works using the following framework:

# 1.	Analysis of the data for estimating the noise variance.
# 2.	First step
# -	Block-matching
# -	Coarse filtering on block stacks
# -	Results aggregation
# 3.	Second step
# -	Block-matching using the noisy data as well as the results of first step
# -	Wiener filtering using the noisy data as well as the results of first step on block stacks
# -	Results aggregation
# The data analysis computes an estimate for the noise parameter sigma (~standard deviation of the noise) as well as a dissimilarity threshold (this threshold is used only when [d_max] is set to 0 (default)). The estimated sigma value will be used for the denoising when no [sigma] value is entered by the user. It is a good starting point, from which the denoising can be carried out a second time with a more appropriate value.

# The block-matching procedure compares reference blocks ([block_size] is typically 8x8 pixels) with all blocks available in a neighborhood (with maximum distance [s_dist]), and sorts the blocks using a similarity estimator from the most similar to the least similar block. The n (e.g. n = 16 or 32) most similar blocks are kept in a group. Reference blocks are defined every [step] pixels.

# The filtering is performed on 3D-transformed groups. All blocks within the group are first 2D-transformed, and a 1D transform is then carried out along the "stack" direction. The 2D transforms in the implemented bm3d software are performed using the discrete cosine transform (DCT), while the 1D transform is performed either using a Haar wavelet transform or a DCT (option [t1d]). The filtering is typically a hard-thresholding in the first step, and a Wiener filter in the second step (see [1-5]). The filtered coefficients are then inverse-transformed.

# Each pixel will typically have been filtered several times in slightly different ways. The aggregation is a weighted average; weights are calculated during the filtering and are inversely proportional to the group variance.

# The bm3d program offers several methods for denoising images:

# The BM3D method corresponds to the algorithm described in [1] and [2]. The filtering can be performed directly on the data, on the square root of the data, or on the log of the data:

# Filtering on the log of the data is relatively well adapted to SAR multilooked intensity images, however it may "eat" some isolated, strongly scattering elements.
# Direct filtering on the data values of SAR multilooked intensity images results in non-homogeneous denoising, with darker areas much more strongly denoised than brighter areas. This method works well for linearly-scaled images or optical images. This method also supports data with negative values or complex data.
# The square root method provides intermediate results for SAR multilooked intensity images.
# The SAR-BM3D method is based on [3] and [4]. It is adapted from [1] to deal with the characteristics of speckle noise in multilooked intensity images. The actual implementation is close to that described in [4]. The denoising results are similar to those obtained when filtering on the log of the data, however with much improved preservation of isolated, strongly scattering elements. Two profiles are available, a "fast" profile (although slightly slower than the BM3D profiles) and a "fine" profile with larger search distance and a larger maximum number of blocks per group.

# The C-BM3D filters color images. It transforms the RGB input image into a YCbCr color space. In both the first and second steps, the block-matching procedure is performed only on the luminance channel, while the filtering is performed separately on each channel. The final result in YCbCr color space is transformed back into RGB values.

# The InSAR-BM3D method is based on [5] and aims at denoising wrapped interferograms. It is adapted from [1] to handle the characteristics of SAR interferometric images. Note that in that case, the [sigma] parameter is a factor multiplying the standard deviation estimated from the data. Entering the accurate number of looks (option [looks]) is crucial for obtaining high quality results with this method. As an approximation to the equivalent number of looks, the multilook factor can be used (e.g.: if 8 looks in azimuth and 2 looks in range were used to generate the interferogram, entering 16 as the equivalent number of looks should not be too far from the actual value).

# bm3d is a computationally demanding algorithm, in case of large images, first tries can be performed on a subset of the image, with the full image being processed when the appropriate method and [sigma] parameter has been found. Another possibility is decreasing the maximum search distance [s_dist]. The Haar transform in the third dimension is also slightly faster than the DCT. 
