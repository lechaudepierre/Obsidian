### Point Processing 

#### Threshold

Using the principle of a look-up table 

$$g_{out} = \begin{cases} 255, & \mbox{if }g_{in}>th \\0, & \mbox{otherwise}\end{cases}$$

There is also **semi-threshold**, the value above the threshold are juste the initial values.  

#### Gamma Correction 

It is a **non-linear** image adjustment method that alters the contrast of an image : 

$$g_{out} = A \, g_{in}^{\gamma}$$

where
$$A = 255^{1−\gamma}$$

If $\gamma < 1$, darker areas get more contrast. 
if $\gamma >1$, brighter regions gain more contrast.

#### Auto-level Adjustment

Used to **enhance the contrast** of an image by mapping the complete dynamic range to full scale of intensity levels : 
$$g_{out} = 255 \, \frac{g_{in} - g_{min}}{g_{max} - g_{min}}$$

#### Histogram equalization

Enhance the contrast of an image by redistributing the pixel intensity levels across the entire range of the image of possible values (create a more uniform histogram). This is particularly beneficial in images where certain features are obscured by poor contrast, allowing for better visual analysis and interpretation. 

****

### Linear filters 

Here, we are not just focusing on one point and making transformation from it, we are using the **neighborhood of the point for changing it to it's new value.** We are using **linear functions** to do that, the new pixel value is a **linear combination of value of pixels from his neighborhood.** 

What happens **close to the borders** ? We use various **padding methods** such as :
- reflecting the borders pixels (miror the image at the borders). 
- filling the outeur regions with 0's.
- replication : extend the values at the borders.
(sometimes, we only apply the structuring elements to pixels that don't have this problem of borders) 

#### Convolution 

The value of each pixel is computed as a weighted sum of its neighborhood, defined by a structuring element. (it involves associating weights with each element of the SE ($\neq$ value of the SE matrix)). 
Example :  
- the mean filter

#### Fourrier domain 

While convolution operates in the spacial domain, the fourrier transform allows us to analyze signals and images in the frequency domain. (particularly useful for **detecting periodic patterns** or **filtering out noise**).
We use the **power spectrum** 

**2D Fourier transform**

**Discrete Fourier transform**

**Fast Fourier transform**
- used to **reduce the time complexity** of computing the **DFT**. 

**Phase and Amplitude**

- Adding noise to amplitude : 
	- affects the intensity of the reconstructed image. 
- Adding noise to phase : 
	- alters the spacial features of the image.


#### Convolution in frequency domain 

Low pass filter via fourrier transform : 
- smoothing the image 
Order of things : 
1. Computing the discrete Fourier transform (DFT) of the image.
2. Applying a filter that keeps only frequencies near the center.
3. Performing the inverse DFT to reconstruct the filtered image.

‼️ filtering the fourrier domain to a form with sharp edges (rectangle) can lead to unwanted oscillations in the reconstruct image ‼️ 

![[Pasted image 20250127111335.png |500]]

To deal with that we use a circular filter with smooth edges (for reducing the oscillations and achieve a more natural low pass filter). 
![[Pasted image 20250127111155.png | 500]]

With just **suppressing the central part of the fourrier spectrum**, we obtain a **high pass filter** (keeping high frequency components corresponding to rapid changes in intensity, such as edges and borders and suppress smooth region (like the sky in the example))
![[Pasted image 20250127111410.png | 500]]

#### Correlation 

Used to identify patterns or features within images. 
$$f(x)\circ g(x) \Leftrightarrow F^*(u)G(u)$$
Allows to perform correlation in the frequency domain, often resulting in faster computations.  
![[Pasted image 20250127112812.png | 500]]


### Non-linear filters

when local processing **deviates from the standard weighted** sum of neighboring pixels, we refer to such methods as **non-linear**.  

Examples : 
- Median filter

The **impact of the radius size** of the structuring element cannot be negligible and must be taken into account. 

**Median VS Mean :** 
- **Median tends to maintains sharper edges** and clearer transitions between regions (preserving important structural details)
- **Mean results in a blurring effect** along borders, which can lead to a loss of detail and definition.

#### Local histogram technique 

Used to **enhance contrast** and **improve the visibility of details** in specific regions of an image. Adjusts pixels values **based on their local context**, making it effective for highlighting features that might be overshadowed in the overall histogram. 

#### Other rank-based filters 

1. Local Maximum and Local Minimum 
They emphasize extremes in pixel intensity within a specified neighborhood. particularly useful for tasks such as edge detection and feature extraction. 
![[Pasted image 20250127131300.png|500]]
Borders can be detected by finding locations where the local maximum transitions to a local minimum (or vice-versa). 

2. Local contrast equalization 
Enhance the visibility of features in an image by adjusting the local brightness levels. it improves contrast in specific regions (particularly useful for images with varying lighting conditions).

When looking a the histogram, it emphasize all variations in intensity : 
![[Pasted image 20250127132634.png|500]]

3. Local auto-level adjustment 
Enhance contrast by redistributing pixel intensity values based on local neighborhoods. 
It **can be used with min-max or with percentile scaling**.
It has almost the same histogram as local contrast equalization.

4. Local morphological contrast enhancement 
applies non-linear operations to improve the visibility of images features by emphasizing local contrasts. Particularly effective for enhancing details in images with varying illumination. 

5. local threshold 
The goal is to differentiate the background and the foreground.
![[Pasted image 20250127133600.png|500]]

#### Others non-linear filters

1. bi-lateral filtering 
Preserves edges while reducing noise.  It operates by considering both a local spatial neighborhood and a spectral (gray-level) neighborhood around each pixel, allowing it to differentiate between noise and important features in the image. This is effective for images with high-frequency details, such as edges, as it reduces noise without blurring these features.  
![[Pasted image 20250127134620.png|500]]

2. Anisotropic filter of Nagao
Designed to smooth the image while preserving edges. This method utilizes five complementary 5x5 filters oriented in different directions to assess local pixel neighborhoods. 
Method :
- **Filter application** : the 5 anisotropic filters are applied to each pixel, capturing informations in various directions.  
- **Mean and Variance calculation** : helps determining the level of smoothing required.
- **Output selection** : The final filtered value for a pixel is selected as the mean from the filter that exhibits the lowest variance, ensuring that the edge information is preserved while reducing noise.
 ![[Pasted image 20250127135245.png|300]]
3. **Diffusion filter**
Smoothing process that mimics natural diffusion, effectively reducing noise while preserving important structural boundaries in an image.
![[Pasted image 20250127135607.png|500]]

4. Non-local image denoising
The filtered image is obtained as a weighted mean of pixels from the neighborhood. the weight assigned to each pixel is based on the similarity of their local gray-level distributions; pixels with similar neighboring gray levels contribute more significantly to the average. 

### Edge detection 

#### Laplacian operator 
Commonly used in image processing to detect edges and highlight regions of rapid intensity change. It is more often represented in a kernel configuration. it is the gradient operator ($\nabla^2$) 
#### Gradient operator 
Measures the rate of intensity change in a given direction. It is a vector of partial derivatives and plays a crucial role in detecting edges and boundaries. 
We can compute the amplitude (magnitude) and angle of the gradient. 
![[Pasted image 20250127141725.png|500]]

#### Gradient amplitude
Represents the rate of intensity change accros the image and is a key operation in edge detection and image analysis. 

1. Robert's operator 
Approximates the gradient by calculating the difference between adjacent pixel pairs : 
$$||\vec \nabla f||= |f(x,y)-f(x+1,y+1)|+|f(x+1,y)-f(x,y+1)|$$
This corresponds to 2 simple convolutions kernels : 
$$\begin{bmatrix}
1 & 0 \\
0& -1 \\
\end{bmatrix}$$
and 
$$\begin{bmatrix}
0 & 1 \\
-1& 0 \\
\end{bmatrix}$$
2. Prewitt Operator 
Detects horizontal and vertical edges using :
$$\begin{bmatrix}
-1 & -1 & -1\\
0 & 0 & 0\\
1 & 1 & 1\\
\end{bmatrix}$$for **vertical edges** 
and 
$$\begin{bmatrix}
-1 & 0 & 1\\
-1 & 0 & 1\\
-1 & 0 & 1\\
\end{bmatrix}$$
for **horizontal edges**

3. Sobel operator  
Similar to Prewitt's but it gives more weight to central pixels : 
$$\begin{bmatrix}
1 & 0 & -1\\
2 & 0 & -2\\
1 & 0 & -1\\
\end{bmatrix}$$
and 
$$\begin{bmatrix}
1 & 2 & 1\\
0 & 0 & 0\\
-1 & -2 & -1\\
\end{bmatrix}$$

#### Morphological Gradient

