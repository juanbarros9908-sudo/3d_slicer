# 3d_slicer
A volumetric imaging dataset of cross-sectional slices through a 3D sphere phantom, generated in JSON and DICOM formats. Each slice captures a 2D cross-section at a fixed Z depth with realistic pixel density gradients and configurable noise. Compatible with standard viewers like 3D Slicer, OsiriX, and Horos. Built with Python, pydicom, and NumPy.

Project structure
3d-slice-project/
  data/
    slices_sphere_z12.json     # Slice point cloud data (JSON)
    dicom_slices/
      slice_001.dcm
      ...
      slice_012.dcm
  src/
    generate_data.py           # Generates JSON slice data
    generate_dicom.py          # Generates DICOM files
  requirements.txt
  README.md

Data formats
JSON
Each record represents a single point on a slice contour and includes:
FieldDescriptionslice_idZ index of the slice (0–11)point_idPoint index within the slicex, y, z3D coordinatesradiusRadial distance from centerangle_degAngle in degreesdensitySimulated tissue density value
DICOM
12 CT-style .dcm files, one per slice, with full metadata tags:
TagValueModalityCTImage size64 × 64 pxBit depth12-bitSlice spacing5 mmPixel spacing1 mm × 1 mmStudy description3D Slice Project

Quickstart
Requirements
bashpip install pydicom numpy
Load JSON data
pythonimport json

with open("data/slices_sphere_z12.json") as f:
    slices = json.load(f)

print(f"{len(slices)} points across {len(set(s['slice_id'] for s in slices))} slices")
Load DICOM volume
pythonimport pydicom
import numpy as np
import glob

files = sorted(glob.glob("data/dicom_slices/*.dcm"))
slices = [pydicom.dcmread(f) for f in files]
volume = np.stack([s.pixel_array for s in slices])

print(f"Volume shape: {volume.shape}")   # (12, 64, 64)
print(f"Pixel range: {volume.min()} – {volume.max()}")
View in 3D Slicer

Open 3D Slicer
Go to File → Add Data
Select the dicom_slices/ folder
Click Load — the sphere volume renders automatically


Configuration
In generate_data.py and generate_dicom.py, the following parameters can be adjusted at the top of each file:
ParameterDefaultDescriptionNUM_SLICES12Number of Z slicesROWS / COLS64Image resolution per slicenoise0.08Gaussian noise levelshapespherePhantom shape (sphere, cylinder, cone, torus)

Compatibility
Tested with:

3D Slicer 5.x
OsiriX / Horos
pydicom 2.x
Python 3.9+
