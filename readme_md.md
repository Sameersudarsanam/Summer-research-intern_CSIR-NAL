# UAV-to-Satellite Visual Geolocalization

> GPS-denied target localization pipeline mapping optical UAV frames to georeferenced satellite base maps using deep feature matching. Developed during an internship at **CSIR-NAL** (UAV Division) by **SSS N Sameer Kumar** (IIT Indore).

---

## 📌 Overview

Unmanned Aerial Vehicles (UAVs) operating in GPS-denied or degraded environments require alternative localization methods to maintain positional awareness. This project implements a vision-based geolocalization system that aligns real-time aerial optical frames with pre-stored, georeferenced satellite base maps (GeoTIFFs). By combining inertial telemetry with deep learning feature matching, the system calculates precise pixel-to-geographic transformations without relying on active satellite navigation signals.

---

## 🎯 Objective

- **GPS-Independent Positioning:** Achieve accurate spatial localization for UAV platforms when GNSS signals are jammed or unavailable.
- **Cross-Modal Image Matching:** Overcome structural, scale, and radiometric discrepancies between oblique aerial camera frames and satellite imagery.
- **Geographic Coordinate Mapping:** Transform camera pixel coordinates $(x, y)$ into global WGS84 coordinates ($\text{Latitude}, \text{Longitude}$).

---

## 🔬 Methodology

1. **Telemetry-Guided Pre-processing:**
   - Onboard IMU data (Roll, Pitch, Yaw) is used to correct lens tilt and rotate UAV frames to align true north with the satellite base map.

2. **Contrast Normalization:**
   - Contrast Limited Adaptive Histogram Equalization (**CLAHE**) normalizes local illumination variations and enhances structural features across modalities.

3. **Dense Feature Correspondence:**
   - Uses a Local Feature Transformer (**LoFTR**) model to extract coarse-to-fine dense correspondences between the optical UAV frame and the reference map.

4. **Robust Outlier Rejection & Homography:**
   - **MAGSAC++** estimates a robust $3 \times 3$ planar homography matrix $H$, eliminating spurious feature matches caused by transient ground objects.

5. **Spatial Projection & Coordinate Lookup:**
   - Applies perspective warping to map pixel points to the spatial extent of the target GeoTIFF raster, resolving target coordinates via geospatial metadata.

---

## 📊 Flowchart

```
                 +-----------------------+
                 | UAV Optical Frame +   |
                 |     IMU Telemetry     |
                 +-----------+-----------+
                             |
                             v
                 +-----------------------+
                 |  Orthorectification & |
                 |     North Alignment   |
                 +-----------+-----------+
                             |
                             v
                 +-----------------------+
                 |   CLAHE Enhancement   |
                 +-----------+-----------+
                             |
                             v
                 +-----------------------+
                 | LoFTR Feature Match   |
                 +-----------+-----------+
                             |
                             v
                 +-----------------------+
                 |  MAGSAC++ Homography  |
                 +-----------+-----------+
                             |
                             v
                 +-----------------------+
                 | Coordinate Conversion |
                 | (Pixel ➔ Lat/Long)    |
                 +-----------------------+
```

---

## 🔄 Workflow

```
[ UAV Video Feed ] ──► [ IMU Alignment ] ──► [ CLAHE Pre-filter ]
                                                     │
[ Satellite GeoTIFF ] ───────────────────────────────┴──► [ LoFTR Matching ]
                                                                 │
                                                                 ▼
[ WGS84 Lat / Lon Output ] ◄── [ GeoTIFF Raster Metadata ] ◄── [ MAGSAC++ Homography ]
```

---

## 🚀 Future Work

- **Digital Elevation Model (DEM) Integration:** Incorporate terrain height maps to account for non-planar terrain relief and minimize parallax errors in high-relief landscapes.
- **Edge Deployment & Optimization:** Quantize and optimize the transformer model using TensorRT or ONNX Runtime for real-time onboard inference on embedded platforms (e.g., NVIDIA Jetson).
- **Multi-Modal Sensor Fusion:** Integrate thermal IR and SAR data to support night-time and adverse weather geolocalization.

---

## 🔑 Key Takeaways

- Combining deterministic physical alignment (IMU tilt/heading correction) with deep feature matching drastically reduces search space and increases match confidence.
- Transformer-based matching models (LoFTR) perform significantly better than classical detectors (SIFT/ORB) on cross-modal, multi-scale aerial-to-satellite imagery.
- Robust geometric consensus algorithms (MAGSAC++) are crucial for filtering false correspondences in dynamic ground environments.