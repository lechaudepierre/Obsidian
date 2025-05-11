
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
***




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

Defined as : 
$$MorphologicalGradient = LocalMaximum - LocalMinimum$$
The result is an edge map that highlights boundaries within the image. 
![[Pasted image 20250127145315.png|500]]
![[Pasted image 20250127145425.png|500]]
1. Top-hat and Bottom-hat transformations :
Enhancing bright and dark objects. 

- Top-Hat Transformation: Extracts bright objects by subtracting the original image from the local maximum : 
$$Top-Hat=Local Maximum−Original Image$$
- Bottom-Hat Transformation: Extracts dark objects by subtracting the local minimum from the original image : 
$$Bottom-Hat=Original Image−Local Minimum$$
![[Pasted image 20250127145756.png|500]]
![[Pasted image 20250127145811.png|500]]

#### Laplacian of Gaussian (LoG) operator 
Combines **Gaussian smoothing** (low pass filter) and **Laplacian filtering** (high pass filter). 
By applying the Laplacian operator to a Gaussian-smoothed image, the **LoG filter highlights regions of rapid intensity change while reducing the effect of noise.**
$\Rightarrow$ Helps detecting fine edges while reducing the impact of noise

#### Difference of Gaussian (DoG) operator
Approximates the **LoG** by subtracting two Gaussian-blurred versions of an image with different standard derivations ($\sigma$). This provides a computationally efficient method to detect edges and details.  

#### Gaussian and Laplacian pyramids 
Used to capture details at different levels of resolution. 

1. Gaussian Pyramid: Successive images are created by progressively smoothing and downscaling an original image.
2. Laplacian Pyramid: Each level is the difference between two successive levels of the Gaussian pyramid, highlighting edges and fine details.


#### Canny edge detection 
It is a multi-step process for detecting edges in an image : 
1. **Image smoothing :** 
A Gaussian filter is applied to the image to smooth out noise and unnecessary details before detecting edges. 

2. **Gradient Intensity detection :**
The intensity and direction of gradients are computed using filters like Sobel. The magnitude gives the strength of edges while the direction indicates where they occur. 

3. **Local Non-Maximum suppression**
Ensures that only the pixels corresponding to the strongest edges are kept, while non-maximum pixels are suppressed, sharpening the edges lines. 

4. **Double Border Intensity Thresholding**
Double threshold is applied to classify pixels as strong or weak edges based on their gradient magnitudes. Strong edges are likely part of the object while weak edges are tentative. (classify the edges in 3 categories : weak, strong, non-edges)

5. **Weak Edge Suppression**
Weak edges are further suppressed unless they are connected to strong edges, helping to clean up spurious or isolated edges. 


Canny VS Sobel : 
- Canny results in more refined edges 
- Canny applies edge thinning and suppression
- While Sobel primarily focuses on detecting gradients. 



### Segmentation 

Aims to partition an image into "objects", defined as connected pixels sharing semantic content.

- **Low-level (Bottom-up)** : aggregate pixels based on shared features (intensity, color or texture). For example pixels that shares a common intensity might be grouped together. 
- **High-Level (Top-Down) :** starts with the complete image and use prior knowledge to identify objects. For example detecting a face in an image to assist in camera auto-focus.  

There are several approaches to segmentation : 

#### Histogram based segmentation
Widely used in applications where pixels intensities directly correspond to physical properties. Used when a pixel value has a direct interpretation. 
Challenges : 
- uneven illumination
- presence of shadows 
- variable brightness or texture

1. **Image Thresholding**
	3 types of thresholds : 
	1. Fixed 
	2. Globally adaptative (take local parts of the image)
	3. Locally adaptative (Otsu, etc)

2. **Percentile Thresholding** 
	Useful when we have prior knowledge about the pourcentage of the image that corresponds to the object of interest. 

3. **Optimal Threshold**
	Computes iteratively the best threshold by splitting the image into two regions based on an initial threshold.
$$T' = \frac{m1 + m2}{2}$$
	1. **Otsu Threshold** 
		Selects the thresholds k that minimizes the intra-class variance (within each class) or maximize the inter-class variance (between the classes). 

	 2. **Entropy Threshold**
		Selects the thresholds that maximize the total entropy, which is the sum of the entropies of the pixel intensities below and above the threshold. By maximizing the entropy, we aim to find the Threshold that best separates the image into meaningful classes. 

4. **Mutli-spectral Threshold**
	Techniques for RGB or HSV images. We treat each color channel separately and apply Otsu -threshold on them and then recombine them. 

5. **Adding spectral dimensions for enhanced segmentation** 
	Useful when an image presents regions of different average gray-levels : 
	![[Pasted image 20250127162428.png|500]]

#### Border based segmentation
- Sobel : can struggle with noise and disconnected borders. 
- Canny : to prevent Sobel's problems, we use this filter. Because of his Gaussian  smoothing, edge detection with with gradient direction analysis and hysteresis thresholding to connect broken edges. 
#### Region based segmentation
Correct the fact that border based may leave incomplete or disconnected boundaries, here we focuses on identifying connected regions in an image. This method searches for homogeneous areas  based on pixel intensity, color, texture ,rather than relying solely on edge detection. 

1. **Region Growing** 
	start by selecting a seed point within the desired object. From this point, neighboring pixels are iteratively added to the region if they satisfy a predefined homogeneity criterion, such as similarity in intensity or color. (Avoid the issue of incomplets contours associated with edge-based methods).

2. **Split and merge**

	Uses a recursive approach to identify homogeneous regions in an image. 
	1. **Split phase** : 
		recursively divided into 4 quadrants based on a homogeneity criterion. if the the sub-image (or entire image) is homogeneous, the recursion stops, and the region is not split further. Otherwise the process continues recursively. 
	2. **Merge phase** : 
		Adjacent sub-images are tested to see if they satisfy the homogeneity criterion. If they do, they are merged into a single region. 
	![[Pasted image 20250127165824.png|500]]

3. **Watershed Transform**
	Threat an image as a topographic surface where pixel intensity represents elevation (only applied on gray-scale images). The algorithm simulates the flooding of water from regional minima, and the watershed lines mark the boundaries between different catchment basins. 

#### Model based segmentation 
In this section, we explore other top-down methods for segmentation, where the process is driven by predefined knowledge about the object. 

1. **Live-wire algorithm**
	interactive method where the user provides some initial input to guide the segmentation process. The user selects seed points along the boundary of the object and the algorithm computes the optimal path between these points using a cost-minimization strategy. 
	The local costs between **P** and **Q** are computed using : 
	1. Gradient magnitude 
	2. Laplacian Zero crossing 
	3. Gradient direction 

	Pros : 
	- general applicability 
	- real-time interaction 
	- direct control
	Cons : 
	- pixel resolution 
	- weight sensitivity
	- slow for 3d images

2. Dijkstra's Shortest path algorithm 
	Known µ

#### Active contour 
La courbe se déforme de façon a minimiser la fonction d'énergie. Elle cherche un compromis entre rester lisse (énergie interne) et coller les bords (énergie externe)
1. Energie interne 
	- Définit le faite que la courbe soit lisse et cohérente (donc évite les changements brusque et formes irrégulières)
	    - Elasticité (donnée par alpha) : empêche la courbure de trop s’étirer et se contracter, maintient la courbe tendue et homogène
	    - Rigidité (donnée par bêta) : Limite les courbures excessive, favorise courbe lisse en empêchant les angles serré
2. Energie externe 
	- C’est l’énergie qui attire la courbe vers des caractéristiques importantes de l’image (comme les bords ou les gradients d’intensité)
	    - Force basée sur le gradient (donnée par gamma):   Attire la courbe vers les zone ou le gradient est grand (variation rapide d’intensité, correspondant généralement aux bords)
	    - Force de pression (Kappa):  Force supplémentaire qui peuvent pousser la courbe vers l’intérieur (pour capturer les objets fermé ) ou vers l’extérieur (pour détecter  des structures plus grandes)

#### Hough Transform
It groups pixels that belong to the same line, even if the line is segmented. 
$$\rho = x_0 \cos(\theta) + y_0 \sin(\theta)$$
1. **We detect the edges** (using Sobel or Canny)
2. **Parameter space voting** : for each edge pixel, we calculate the possible ($\theta$, $\rho$) values and plot the corresponding sinusoidal curves in the ($\theta$, $\rho$) parameter space 
3. **Find peaks in parameter space** : points where the sinusoidal curves intersect ($\theta_0$, $\rho_0$)

Comme c’est un espace espace discret , la résolution (delta(rho) et delta(thêta)) aura un impact la précision des droites détectées