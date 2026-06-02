# Cell Counting by Threshold and Watershed Segmentation

This project counts fluorescently labeled cells in TIFF microscopy images using a classical image segmentation pipeline.

## Method

The algorithm uses the following steps:

1. Read a TIFF microscopy image.
2. Select the target color channel.
   - NeuN images use the blue channel.
   - Olig2 images use the green channel.
   - If the marker name is unknown, the brightest RGB channel is selected automatically.
3. Smooth the selected channel with a Gaussian filter to reduce noise.
4. Use Otsu thresholding to separate bright cell signal from background.
5. Adjust the threshold factor depending on the marker.
6. Remove small noisy objects and fill small holes.
7. Use distance transform and watershed segmentation to separate touching cells.
8. Count each segmented region as one cell.
9. Save an overlay image showing detected cell boundaries.

## Installation

Install the required Python packages:

```bash
python -m pip install -r requirements.txt
```

If your system uses `python3` instead of `python`, run:

```bash
python3 -m pip install -r requirements.txt
```

Anaconda can also be used. For example:

```bash
conda create -n cellcount python=3.11
conda activate cellcount
python -m pip install -r requirements.txt
```

## How to Run

Put the TIFF image in the same folder as `main.py`, then run:

```bash
python main.py "image_name.tif"
```

Example:

```bash
python main.py "9794 NeuN.tif"
```

If using `python3`:

```bash
python3 main.py "9794 NeuN.tif"
```

The quotation marks are important when the filename contains spaces.

## Output

The script prints the selected channel and cell count:

```text
Using channel: blue
Cell count: 616
```

It also saves an overlay image:

```text
9794 NeuN_overlay.png
```

The overlay image shows the original microscopy image with red segmentation boundaries drawn on top.

## Parameters

The script uses different parameters for NeuN and Olig2 images:

| Marker | Channel | Threshold Factor | Minimum Object Size | Watershed Min Distance |
|---|---|---:|---:|---:|
| NeuN | Blue | 0.85 | 45 | 7 |
| Olig2 | Green | 0.95 | 20 | 10 |
| Unknown | Auto brightest channel | 1.00 | 30 | 8 |

Lower threshold factors detect dimmer cells but may include more background noise. Higher threshold factors are stricter and detect only brighter cells.

## Notes

This is a classical segmentation baseline. It works best when cells are brighter than the background and have reasonably separated shapes. Dense or blurry images may require parameter tuning or a deep learning segmentation method such as Cellpose.
