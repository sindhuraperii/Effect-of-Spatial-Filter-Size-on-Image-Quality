# 🎚️ Effect of Spatial Filter Size on Image Quality

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?logo=opencv&logoColor=white)](https://opencv.org/)
[![scikit-image](https://img.shields.io/badge/scikit--image-F7931E)](https://scikit-image.org/)
[![NumPy](https://img.shields.io/badge/NumPy-013243?logo=numpy&logoColor=white)](https://numpy.org/)
[![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-11557C?logo=matplotlib&logoColor=white)](https://matplotlib.org/)

A classical image processing case study that measures how kernel size controls the trade-off between noise removal and detail preservation for mean, Gaussian, and median filters.

🔗 **[Open the Colab Notebook](https://colab.research.google.com/drive/13SMZopYFUTqsGQYvKgcQYy9JCMkhcJse)** 

## 🚀 Executive Summary

Spatial filters clean up noisy images by replacing each pixel with a value computed from its neighborhood. A larger window removes more noise, but it also averages across real edges. This project asks a practical question: when the filter window grows, how much noise is removed, and how much detail is lost?

Instead of judging the results by eye alone, the study compares every filtered image with the clean original using PSNR and SSIM, and measures edge detail with a Laplacian sharpness score.

## 🎯 What It Solves

Choosing a kernel size is often guesswork. Common problems include:

- A kernel that is too small leaves most of the noise in place.
- A kernel that is too large blurs edges, texture, and fine detail.
- Different noise types respond to different filters, so one setting rarely works for every image.
- Quality scores such as PSNR do not always match what a person sees.

This study provides measured answers for one standard test image and two common noise types.

## 💡 What You'll See

- 🖼️ Clean and noisy versions of a standard test image, generated with a fixed random seed
- 🧹 Three spatial filters tested at six kernel sizes (3×3 to 15×15)
- 📏 Objective quality scores (PSNR, SSIM) and a sharpness measure for each result
- 📈 Plots of quality against kernel size for both noise types
- 👀 A side-by-side visual comparison at one kernel size
- 📄 A results table exported to CSV

## 🔁 Core Workflow

1. Load the clean grayscale cameraman image (512 × 512 pixels).
2. Create a Gaussian-noise version (σ = 25) and a salt-and-pepper version (5% of pixels corrupted).
3. Apply each of the 3 filters at each of the 6 kernel sizes to each noisy image, giving 2 × 3 × 6 = 36 filtered outputs.
4. Measure each output against the clean original, plus a baseline measurement of each noisy image with no filtering, for 38 rows in total.
5. Organize the results in a table and plot PSNR and SSIM against kernel size.
6. Compare the images visually at one kernel size and interpret the numbers.

## 🧱 Pipeline Architecture

```text
Clean Image (scikit-image camera)
        ↓
Noise Generation (Gaussian σ = 25 · Salt & Pepper 5%)
        ↓
Spatial Filters (Mean · Gaussian · Median) × Kernel Sizes (3 to 15)
        ↓
Quality Metrics (PSNR · SSIM · Laplacian Sharpness)
        ↓
Results Table (38 rows) → Plots → Visual Comparison → CSV
```

## 🧠 Methodology

This is a controlled experiment. One clean image is degraded with two noise types, filtered with three filters at six kernel sizes, and measured against the clean original. All variables except the one being studied are held constant. The study uses classical image processing algorithms, not machine learning.

### Filters

The mean filter averages each k × k neighborhood:

$$g(x,y) = \frac{1}{k^2} \sum_{s,t} f(x+s,\, y+t)$$

The Gaussian filter uses center-weighted averaging, so nearby pixels count more than distant ones:

$$w(s,t) = \frac{1}{2\pi\sigma^2} e^{-\frac{s^2+t^2}{2\sigma^2}}$$

OpenCV derives σ from the kernel size when σ is set to 0.

The median filter is nonlinear. Its output is the median of the neighborhood values, so it selects an existing pixel value instead of averaging.

### Quality Metrics

| Metric | Formula | Interpretation |
|---|---|---|
| MSE | $\frac{1}{MN}\sum(f-g)^2$ | Average squared pixel error (lower is better) |
| PSNR | $10\log_{10}\frac{255^2}{MSE}$ | Signal-to-noise ratio in dB (higher is better) |
| SSIM | Luminance, contrast, and structure comparison | Structural similarity, from -1 to 1 (1 = identical) |
| Sharpness | Variance of the Laplacian $\nabla^2 g$ | Edge detail retained (higher = sharper) |

Structural similarity (SSIM) compares luminance, contrast, and structure in local windows and averages the result. Edges produce large Laplacian responses, so a higher variance means more fine detail survives filtering.

### Experimental Design

| Type | Variables |
|---|---|
| Independent | Filter type (3), kernel size (6: 3, 5, 7, 9, 11, 15), noise type (2) |
| Dependent | PSNR, SSIM, sharpness |
| Controls | Same image, fixed seed (42), same noise levels, same clean reference |

Odd kernel sizes are used so that each window has a central pixel.

## 🗃️ Dataset

| Property | Value |
|---|---|
| Name | Camera (cameraman) |
| Source | `skimage.data.camera()` (built into scikit-image; no download needed) |
| Size | 512 × 512 pixels |
| Type | 8-bit grayscale (`uint8`), 256 intensity levels (0–255) |
| Content | Smooth regions (sky) and fine detail (tripod, building edges, clothing) |

The noisy versions are generated in the code:

| Noise type | Parameters |
|---|---|
| Gaussian | Mean 0, standard deviation σ = 25 |
| Salt & pepper | 5% of pixels corrupted (2.5% black, 2.5% white) |

A fixed random seed (42) keeps all results repeatable.

## 🦾 Tech Stack

| Tool | Role in this project | Reference |
|---|---|---|
| Google Colab | Browser-based Python environment (CPU runtime is enough) | [10] |
| Python 3 | Programming language | — |
| NumPy | Image arrays and random noise generation | [5] |
| OpenCV (`cv2`) | Mean, Gaussian, and median filters; Laplacian for sharpness | [3], [8] |
| scikit-image | Sample image and PSNR/SSIM metrics | [4], [9] |
| pandas | Results table and CSV export | [7] |
| Matplotlib | Image display and line plots | [6] |
| GitHub | Hosts the code, notebook, results, and this README | — |

## 📂 Project Structure

```text
Effect-of-Spatial-Filter-Size-on-Image-Quality/
├── README.md                        # This file
├── Spatial_Filter_Size_Study.ipynb  # Full notebook with comments on every line
├── filter_size_results.csv          # Results table (38 rows)
└── images/                          # Output screenshots used in this README
    ├── 01_step2_image_info.png
    ├── 02_results_table.png
    ├── 03_psnr_ssim_plots.png
    └── 04_visual_comparison.png
```

## ⚙️ Setup

### Option 1: Google Colab (recommended)

1. Click the **Open In Colab** badge at the top of this README.
2. Click **Runtime → Run all**.
3. Results, plots, and the CSV file appear as each cell finishes.

No installation is required, and a CPU runtime is enough.

### Expected Output

- Step 2 prints the image shape `(512, 512)` and dtype `uint8`.
- Step 6 produces a table with 38 rows and six columns: Noise, Filter, Kernel, PSNR, SSIM, and Sharpness.
- Step 7 draws the 2 × 2 plot grid, and Step 8 draws the visual comparison.
- Step 9 saves and downloads `filter_size_results.csv`.

## 📊 Results

### Output Screenshots

**Image information (Step 2):**

<img width="599" height="58" alt="image" src="https://github.com/user-attachments/assets/3ace98a5-be12-4862-b450-fd2426cee07f" />



**Results table (Step 6):**

<img width="3493" height="1569" alt="image" src="https://github.com/user-attachments/assets/d9e32869-1d7c-4d0b-ad20-d2d20f4cd475" />



**PSNR and SSIM versus kernel size (Step 7):**

<img width="1189" height="790" alt="image" src="https://github.com/user-attachments/assets/eeeb9a2b-ea67-4a65-9e06-9be3a3b20560" />



**Visual comparison at 7 × 7 (Step 8):**

<img width="1415" height="282" alt="image" src="https://github.com/user-attachments/assets/3b8184a0-a305-4db0-8eaf-4464e24c01db" />



### Summary of Best Results

| Noise | Filter | Best kernel | PSNR (dB) | SSIM | Sharpness |
|---|---|---|---|---|---|
| Gaussian | Mean | [TBD] | [TBD] | [TBD] | [TBD] |
| Gaussian | Gaussian | [TBD] | [TBD] | [TBD] | [TBD] |
| Gaussian | Median | [TBD] | [TBD] | [TBD] | [TBD] |
| Salt & pepper | Mean | [TBD] | [TBD] | [TBD] | [TBD] |
| Salt & pepper | Gaussian | [TBD] | [TBD] | [TBD] | [TBD] |
| Salt & pepper | Median | [TBD] | [TBD] | [TBD] | [TBD] |


## 🔍 Key Findings

### Sharpness

For all three filters, the Laplacian variance should fall steadily as the kernel grows. Averaging and median selection suppress the high-frequency content that makes edges sharp. This is the clearest demonstration of the trade-off.

### Gaussian Noise

PSNR and SSIM should rise as the kernel grows, reach a peak at a moderate size, and then fall as blurring starts to dominate. Write down the exact peak, for example: "For Gaussian noise, the Gaussian filter achieved its highest PSNR of [value] dB at [k × k]." The mean and Gaussian filters should perform similarly, with the Gaussian filter often slightly ahead because its center-weighted averaging produces smoother transitions. The median filter is usually weaker here because it is designed for impulse noise, not for additive Gaussian noise.

### Salt-and-Pepper Noise

The mean and Gaussian filters spread the bright and dark impulses into their neighbors, leaving gray smudges, so their scores stay low even at larger sizes. The median filter removes the impulses far more effectively, often reaching its best score at a small size (3×3 or 5×5). Beyond that, it loses fine detail.

### Metric Disagreement

SSIM often peaks at a different kernel size than PSNR because SSIM is more sensitive to structural loss. If the two metrics disagree, note it and explain which one better matches what you see in the visual comparison. Wang et al. [2] discuss why purely numerical metrics can miss perceived quality, so this is worth discussing.

## 🧩 Strengths and Weaknesses

| Filter | Strengths | Weaknesses |
|---|---|---|
| Mean | Simplest and fastest. Effective against mild Gaussian noise. | Blurs edges strongly. Spreads impulse noise into neighbors. |
| Gaussian | Smooth, natural-looking output. Center-weighted, so it blurs edges somewhat less than the mean filter. Good general-purpose choice. | Still blurs edges. Requires choosing σ. Slightly more computation. |
| Median | Excellent for salt-and-pepper noise. Preserves edges better than linear filters. | Weak against Gaussian noise. More computation per pixel. Can remove fine texture and sharp corners. |

## 💡 Insights

The best kernel size depends on the noise type and strength, so no single size is universally best. Matching the filter to the noise matters more than simply increasing the kernel size. A practical rule is to choose the smallest kernel that gives acceptable noise reduction, because every increase beyond that costs detail.

## ⚠️ Limitations

The study uses one image and one noise level for each type, so the conclusions may not generalize. The PSNR and SSIM metrics require the clean original, which is not available in real applications. The study also does not measure processing time, which would be a useful extension since computation grows with k².

## 🏁 Conclusion

This study examined how spatial filter size affects image quality. Mean, Gaussian, and median filters were applied at kernel sizes from 3×3 to 15×15 to images corrupted by Gaussian and salt-and-pepper noise. Quality was measured with PSNR, SSIM, and a Laplacian-based sharpness measure. The results confirmed that increasing kernel size improves noise suppression up to a point, after which edge detail and sharpness are lost. For Gaussian noise, the best results came from [filter] at [size]. For salt-and-pepper noise, the median filter at [size] was most effective. The findings show that the optimal kernel size depends on both the filter type and the noise type, and that a small, well-matched filter often provides the best balance between noise removal and detail preservation.

**Future work.** Future extensions include measuring processing time for each filter and kernel size, testing several images and noise levels, comparing against adaptive filters such as bilateral filtering and non-local means, and evaluating with a perceptual metric alongside PSNR and SSIM.

## 📚 References

1. R. C. Gonzalez and R. E. Woods, *Digital Image Processing*, 4th ed. Pearson, 2018.
2. Z. Wang, A. C. Bovik, H. R. Sheikh, and E. P. Simoncelli, "Image quality assessment: From error visibility to structural similarity," *IEEE Transactions on Image Processing*, vol. 13, no. 4, pp. 600–612, Apr. 2004.
3. G. Bradski, "The OpenCV Library," *Dr. Dobb's Journal of Software Tools*, 2000. [https://opencv.org](https://opencv.org)
4. S. van der Walt et al., "scikit-image: Image processing in Python," *PeerJ*, vol. 2, p. e453, 2014.
5. C. R. Harris et al., "Array programming with NumPy," *Nature*, vol. 585, pp. 357–362, 2020.
6. J. D. Hunter, "Matplotlib: A 2D graphics environment," *Computing in Science & Engineering*, vol. 9, no. 3, pp. 90–95, 2007.
7. W. McKinney, "Data structures for statistical computing in Python," in *Proc. 9th Python in Science Conf.*, 2010, pp. 56–61.
8. OpenCV Documentation, "Smoothing Images" tutorial. [https://docs.opencv.org](https://docs.opencv.org)
9. scikit-image Documentation, "skimage.metrics" module. [https://scikit-image.org/docs/stable/api/skimage.metrics.html](https://scikit-image.org/docs/stable/api/skimage.metrics.html)
10. Google Colaboratory. [https://colab.research.google.com](https://colab.research.google.com)

## 👥 Authors

**Gayathri Sindhura Peri** 
