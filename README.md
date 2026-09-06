# Image Denoising Using Gaussian Low-Pass Filter

This project studies **image denoising using a Gaussian low-pass filter**. The objective is to reduce noise in a set of noisy images while retaining important image structures.

A Gaussian filter performs spatial smoothing by giving higher weights to pixels closer to the center of the filter window. Since image noise generally contains significant high-frequency components, Gaussian low-pass filtering suppresses these components and produces a smoother image.

The experiments evaluate different values of the Gaussian standard deviation `σ` and determine the best filtering parameter for each noisy image using **Mean Squared Error (MSE)** and **Structural Similarity Index (SSIM)**.



## Method

The noisy image is represented as:

$$
y(m,n)
$$

where `m` and `n` denote the spatial coordinates.

The denoised image is obtained by convolving the noisy image with a Gaussian kernel:

$$
\hat{x}(m,n) = \sum_k \sum_l w(k,l)y(m+k,n+l)
$$

where:

* \(y(m,n)\) is the noisy image.
* x^(m,n)is the denoised image.
* \(w(k,l)\) is the Gaussian filter weight.
* `k` and `l` represent the spatial offsets within the filter window.

### Gaussian Kernel

The Gaussian weights are defined as:

$$
w(k,l) = C\exp\left(-\frac{k^2+l^2}{2\sigma^2}\right)
$$


where:

* \(\sigma\) is the standard deviation of the Gaussian distribution.
* `C` is a normalization constant.
* A larger `σ` produces stronger smoothing.

The kernel is normalized such that:

$$
\sum_k\sum_l w(k,l)=1
$$

This ensures that the overall intensity level is approximately preserved during filtering.


## Experimental Setup

### Reference Image

The notebook uses:

```text
img167.bmp
```

as the reference or ground-truth image.

The reference image is resized to:

```text
512 × 512
```

All noisy images are resized to the same dimensions before evaluation.

### Noisy Images

The following five images are processed:

```text
img108.bmp
img125.bmp
img137.bmp
img6.bmp
img32.bmp
```

### Gaussian Filter

An **11 × 11 Gaussian kernel** is used throughout the experiment.

The tested values of `σ` are:

```text
0.1, 1, 2, 4, 8
```

The main parameter being varied is therefore the Gaussian standard deviation `σ`.


## RGB Channel Processing

For color images, the Gaussian filter is applied independently to the three color channels.

The processing is:

```text
Input RGB Image
       |
       v
Split into B, G, R channels
       |
       +----> Gaussian Filter ----> B filtered
       |
       +----> Gaussian Filter ----> G filtered
       |
       +----> Gaussian Filter ----> R filtered
       |
       v
Merge filtered channels
       |
       v
Denoised RGB Image
```

This allows the same spatial filtering operation to be applied separately to each channel before reconstructing the color image.

## Evaluation Metrics

### Mean Squared Error

MSE measures the average squared difference between the denoised image and the reference image.

$$
MSE = \frac{1}{N}\sum_{i=1}^{N}(I_i-\hat{I}_i)^2
$$

where:

* \(I_i\) is the reference pixel value.
* \(\hat{I}_i\) is the corresponding denoised pixel value.
* `N` is the total number of pixel values.

A **lower MSE** indicates that the denoised image is numerically closer to the reference image.

### Structural Similarity Index

SSIM evaluates structural similarity between two images.

It considers characteristics such as:

* luminance
* contrast
* structural information

SSIM generally ranges from lower similarity to a maximum value of `1`, with values closer to `1` indicating greater structural similarity.

In this experiment, SSIM is calculated for the RGB image using:

```python
ssim(filtered_rgb, reference_image, channel_axis=2, ...)
```

---

## Experimental Procedure

For every noisy image, the following procedure is performed:

1. Load the noisy image.
2. Resize it to `512 × 512`.
3. Split the image into B, G, and R channels.
4. Apply the Gaussian filter using each candidate `σ`.
5. Merge the filtered channels.
6. Calculate MSE against the reference image.
7. Calculate SSIM against the reference image.
8. Select the `σ` producing the lowest MSE.
9. Store the corresponding MSE and SSIM.
10. Visualize the original noisy image and its best denoised result.

The overall experimental flow is:

```text
Reference Image
      |
      v
Resize to 512 × 512
      |
      |
Noisy Images
      |
      v
Resize to 512 × 512
      |
      v
Split B, G, R
      |
      v
Test σ = 0.1, 1, 2, 4, 8
      |
      v
Gaussian Filtering
      |
      v
Merge RGB Channels
      |
      +------------------+
      |                  |
      v                  v
     MSE                SSIM
      |                  |
      +--------+---------+
               |
               v
        Select Best σ
               |
               v
       Denoised Image
```

---

## Results

The experiment produced the following best parameter values when selecting the minimum MSE:

| Image        | Best σ |  Best MSE | Best SSIM |
| ------------ | -----: | --------: | --------: |
| `img108.bmp` |    1.0 |  159.8169 |    0.6520 |
| `img125.bmp` |    0.1 |   15.6248 |    0.9004 |
| `img137.bmp` |    4.0 | 1600.0621 |    0.1714 |
| `img6.bmp`   |    0.1 |   49.9358 |    0.7650 |
| `img32.bmp`  |    1.0 |  204.3075 |    0.5638 |

The best `σ` is not identical for every image, indicating that the appropriate amount of Gaussian smoothing depends on the characteristics of the individual noisy image.

---

## Results and Plots

The notebook generates several visual results.

### 1. Noisy vs. Denoised Images

For each of the five input images, the notebook displays:

* Original noisy image
* Corresponding denoised image using the selected optimal `σ`

This provides a direct visual comparison of the effect of Gaussian filtering.

A representative plot can be added to the repository here:

<img width="961" height="2490" alt="image" src="https://github.com/user-attachments/assets/a48abf52-4959-4f31-ba89-bc49b6a417f5" />




### 2. Optimal Sigma vs. Noise Level

The notebook also plots:

```text
Optimal Sigma vs. Noise Level
```

The images are ordered according to their MSE relative to the reference image, and the corresponding optimal `σ` values are plotted.

This plot helps visualize how the required amount of smoothing changes as the noise characteristics of the images change.

<img width="691" height="470" alt="image" src="https://github.com/user-attachments/assets/89db6f3e-0cc4-48e1-bc5e-92778698881a" />


---

### 3. MSE vs. Noise Level

The notebook plots the MSE obtained using the selected optimal `σ` for each image.

<img width="704" height="470" alt="image" src="https://github.com/user-attachments/assets/8322fd65-dede-4072-a04c-6f4af3674203" />

Lower MSE indicates closer numerical agreement between the denoised image and the reference image.

---

## Key Observations

| Observation                        | Finding                                                                                                                                                             |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Effect of Gaussian filtering**   | Gaussian low-pass filtering reduces high-frequency variations and produces smoother images.                                                                         |
| **Effect of σ**                    | Increasing `σ` increases the degree of smoothing.                                                                                                                   |
| **Optimal σ varies across images** | Different noisy images require different amounts of smoothing to obtain the best result.                                                                            |
| **Low σ**                          | Small values such as `0.1` provide relatively weak smoothing and can be effective when the image contains comparatively less noise.                                 |
| **High σ**                         | Larger values such as `4` provide stronger smoothing and can be beneficial for heavily corrupted images, although excessive smoothing can remove useful structures. |
| **MSE**                            | MSE provides a pixel-level numerical measure of the difference between the denoised and reference images.                                                           |
| **SSIM**                           | SSIM provides a structural similarity measure and complements the pixel-level MSE evaluation.                                                                       |
| **Image-dependent performance**    | The same Gaussian parameter does not necessarily produce the best result for every noisy image.                                                                     |
| **Trade-off**                      | Increasing smoothing can reduce noise but may also blur edges and fine image details.                                                                               |

---

## Interpretation of the Results

The results demonstrate that Gaussian denoising involves a trade-off between **noise suppression and detail preservation**.

For example, `img125.bmp` obtains its best result with a relatively small:

$$
\sigma = 0.1
$$

and achieves:

$$
MSE = 15.6248
$$

$$
SSIM = 0.9004
$$

This indicates a strong match with the reference image under the selected evaluation criteria.

In contrast, `img137.bmp` obtains its minimum MSE using:

$$
\sigma = 4.0
$$

with:

$$
MSE = 1600.0621
$$

and:

$$
SSIM = 0.1714
$$

This shows that a larger smoothing parameter was selected for this image, but the resulting reconstruction remains substantially different from the reference image.

Therefore, the optimal Gaussian parameter depends strongly on the noise characteristics and content of each image.
