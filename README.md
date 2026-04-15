# Fluorescence Microscopy Particle Analysis — ImageJ Protocol

## Objective

The objective of this analysis was to count and characterise fluorescent protein particles (ER-localised protein puncta/foci) visible in a fluorescence microscopy image using ImageJ (Fiji). The analysis aimed to quantify the number, size, and shape of individual protein particles by applying background subtraction, brightness/contrast adjustment, thresholding, and automated particle detection. All measurements were performed in pixel units as no scale bar was available in the image.

---

## Image Source & Metadata

| Parameter | Details |
|---|---|
| Source | OpenCell (czbiohub.org) |
| Image type | Fluorescence microscopy (confocal) |
| Channel used | Red channel (ER protein signal) |
| Original format | PNG (converted to TIF after channel split) |
| Scale bar | Not available — measurements in pixels |
| Pre-processing by source | None — raw image used |

The original image was saved in the `data/raw/` folder without any modification.

---

## Step 0: Image Acquisition

I downloaded a raw fluorescence microscopy image from OpenCell (czbiohub.org), a publicly available fluorescence microscopy database. The image showed intracellular protein puncta/foci — distinct, countable fluorescent particles — making it appropriate for particle counting analysis. I confirmed that the image was not pre-processed and that individual particles were clearly distinguishable from the background.

The original image was saved in the `data/raw/` folder without modification.

---

## Step 1: Opening and Pre-processing the Image

**1a. Open the image in Fiji**

```
File → Open → [select image file]
```

**1b. Split channels**

Since the original image was a multi-channel fluorescence image (RGB), I first split it into individual channels:

```
Image → Color → Split Channels
```

This produced three separate grayscale images (C1 = Blue, C2 = Red, C3 = Green). I selected the **Red channel (C2)** as it showed the ER protein signal most clearly. The blue and green channel windows were closed.

**1c. Convert to 8-bit grayscale**

```
Image → Type → 8-bit
```

This standardised the image to an 8-bit format (pixel values 0–255), which is required for thresholding and particle analysis.

**1d. Subtract background**

To reduce uneven illumination and improve particle visibility:

```
Process → Subtract Background
```

- Rolling Ball Radius: **12 pixels**
- Light background option: **Not selected** (background is dark/black)

**1e. Adjust brightness and contrast**

To improve the visibility of the protein particles before thresholding:

```
Image → Adjust → Brightness/Contrast
```

I first clicked **Auto** to let Fiji suggest appropriate levels, then manually fine-tuned the sliders to make the bright protein dots more clearly visible without overexposing the image. This step was performed for visual clarity only and did not permanently alter pixel values before thresholding.

---

## Step 2: Thresholding

To separate the fluorescent protein particles from the background, I applied thresholding to convert the image to binary (black and white):

```
Image → Adjust → Threshold
```

- Method used: **Default**
- Lower threshold value: **69**
- Upper threshold value: **81**
- Display mode: **Black & White**

I adjusted the threshold sliders until the protein particles appeared clearly highlighted while minimising background noise and avoiding selection of large cell body outlines. After achieving a satisfactory selection, I clicked **Apply** to convert the image to a binary format.

> **Note on Auto-threshold:** I also explored the auto-threshold options available under `Image → Adjust → Auto Threshold` (methods: Default, Otsu, Triangle, etc.) for comparison. The manual threshold values of 69–81 gave the best particle coverage for this image.

**Convert to Mask:**

```
Process → Binary → Convert to Mask
```

**Watershed segmentation:**

Since some particles were touching or clustered, I applied watershed segmentation to separate them:

```
Process → Binary → Watershed
```

This introduced 1-pixel boundaries between adjacent particles, helping to count them individually.

---

## Step 3: Setting Measurements

Since the image did not contain a scale bar, all measurements were performed in pixel units. I selected the parameters required for analysis:

```
Analyze → Set Measurements
```

The following measurements were selected:

- ✅ Area
- ✅ Perimeter
- ✅ Mean Gray Value
- ✅ Shape Descriptors
- ✅ Feret's Diameter
- ✅ Display Label

---

## Step 4: Analyze Particles

```
Analyze → Analyze Particles
```

Parameters used:

| Parameter | Value |
|---|---|
| Size (pixels²) | 5 – Infinity |
| Circularity | 0.20 – 1.00 |
| Show | Outlines |
| Display results | ✅ Checked |
| Summarize | ✅ Checked |
| Exclude on edges | ✅ Checked |
| Add to Manager | ✅ Checked |
| Include holes | ⬜ Not checked |

- **Size 5–Infinity:** Excluded very small noise pixels below 5 pixels² while retaining all genuine protein particles.
- **Circularity 0.20–1.00:** Allowed for detection of both circular and slightly elongated particles (1.00 = perfect circle, 0.00 = completely irregular).
- **Exclude on edges:** Particles cut off at the image border were excluded to avoid partial measurements.

Fiji generated a **Results Table** (individual particle data) and a **Summary Table** (total count and averages), both of which were saved as CSV files.

---

## Results Summary

| Parameter | Value |
|---|---|
| Total particles counted | 98 |
| Total area (pixels²) | 592 |
| Average particle size (pixels²) | 6.04 |
| Average circularity | 0.52 |

---

## Files Saved

| Folder | File | Description |
|---|---|---|
| `data/raw/` | `ER_image_original.png` | Original unmodified image from OpenCell |
| `analysis/` | `README.md` | This protocol document |
| `results/` | `Results_particles.csv` | Individual measurements for all 98 particles |
| `results/` | `Summary_table.csv` | Summary statistics (count, area, circularity) |
| `results/` | `result_image_outlines.tif` | Binary image with particle outlines drawn by Fiji |
