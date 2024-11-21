# V2X-R: Cooperative LiDAR-4D Radar Fusion for 3D Object Detection with Denoising Diffusion 
[![paper](https://img.shields.io/badge/arXiv-Paper-<COLOR>.svg)](https://arxiv.org/abs/2411.08402)
[![code](https://img.shields.io/badge/dataset-link-blue)](http://39.98.109.195:1000/)
<div align="center">
  <img src="images/logo.png" width="200"/>
</div>



## :balloon: Introduction
:wave: This is the official repository for the V2X-R, including the V2X-R dataset and the implementation of the benchmark model, and MDD module. 

This repo is also a unified and integrated multi-agent collaborative perception framework for **LiDAR-based**, **4D radar-based**, **LiDAR-4D radar fusion** strategies!

### Features

- :sparkles: Dataset Support
  - [x] V2X-R
  - [x] OPV2V
  - [x] DAIR-V2X

- :sparkles: Modality Support
  - [x] LiDAR
  - [x] 4D Radar
  - [x] LiDAR-4D Radar Fusion
    
- :sparkles: SOTA collaborative perception method support
    - [x] Late Fusion
    - [x] Early Fusion
    - [x] [When2com (CVPR2020)](https://arxiv.org/abs/2006.00176)
    - [x] [V2VNet (ECCV2020)](https://arxiv.org/abs/2008.07519)
    - [x] [PFA-Net (ITSC2021)](https://ieeexplore.ieee.org/abstract/document/9564754)
    - [x] [RTNH (NIPS2022)](https://arxiv.org/abs/2206.08171)
    - [x] [DiscoNet (NeurIPS2021)](https://arxiv.org/abs/2111.00643)
    - [x] [V2X-ViT (ECCV2022)](https://arxiv.org/abs/2203.10638)
    - [x] [CoBEVT (CoRL2022)](https://arxiv.org/abs/2207.02202)
    - [x] [Where2comm (NeurIPS2022)](https://arxiv.org/abs/2209.12836)
    - [x] [CoAlign (ICRA2023)](https://arxiv.org/abs/2211.07214)
    - [x] [BM2CP (CoRL2023)](https://arxiv.org/abs/2310.14702)
    - [x] [SCOPE (ICCV2023)](https://arxiv.org/abs/2307.13929)
    - [x] [How2comm (NeurIPS2023)](https://openreview.net/pdf?id=Dbaxm9ujq6)
    - [x] [InterFusion (IROS2023)](https://ieeexplore.ieee.org/document/9982123)
    - [x] [L4DR (Arxiv2024)](https://arxiv.org/abs/2408.03677)
    - [x] [SICP (IROS2024)](https://arxiv.org/abs/2312.04822)

- Visualization
  - [x] BEV visualization
  - [x] 3D visualization

## :balloon: V2X-R Dataset Manual 
The first V2X dataset incorporates LiDAR, camera, and **4D radar**. V2X-R contains **12,079 scenarios** with **37,727 frames of LiDAR and 4D radar point clouds**, **150,908 images**, and **170,859 annotated 3D vehicle bounding boxes**.
<div align="center">
  <img src="images/radar_sup.png" width="600"/>
</div>



### Dataset Collection
Thanks to the [CARLA](https://github.com/carla-simulator/carla) simulator and the [OpenCDA](https://github.com/ucla-mobility/OpenCDA) framework, our V2X-R simulation dataset was implemented on top of them. In addition, our dataset route acquisition process partly references [V2XViT](https://github.com/DerrickXuNu/v2x-vit), which researchers can reproduce according to the data_protocol in the dataset.

### Download and Decompression
:ledger: Log in [here](http://39.98.109.195:1000/) using the username "Guest" and the password "guest_CMD" to download the dataset.


Since the data is large (including 3xLiDAR{normal, fog, snow}, 1xradar, 4ximages for each agent). We have compressed the sequence data of each agent, you can refer to this code for batch decompression after downloading.
```python
import os
import subprocess
def decompress_v2x_r(root_dir, save_dir):
    for root, dirs, files in os.walk(root_dir):
        for file in files:
            if file.endswith('.7z'):
                file_path = os.path.join(root, file)
                path = root.split('/')
                extract_path = os.path.join(save_dir, path[-2], path[-1], file[:-3])
                os.makedirs(extract_path, exist_ok=True)
                subprocess.run(['7z', 'x', '-o' + extract_path + '/', file_path])

data_directory = #downloaded dataset path e.g: '/mnt/16THDD-2/hx/V2X-R_Dataset(compressed)'
output_directory =  #output dataset path  e.g: '/mnt/16THDD-2/hx/t'
decompress_v2x_r(data_directory, output_directory)
```

### Structure
:open_file_folder: After download and decompression are finished, the dataset is structured as following:

```sh
V2X-R # root path of v2x-r output_directory 
├── train
│   ├──Sequence name (time of data collection, e.g. 2024_06_24_20_24_02)
│   │   ├──Agent Number ("-1" denotes infrastructure, otherwise is CAVs)
│   │   │   ├──Data (including the following types of data)
│   │   │   │ Timestamp.Type, eg.
│   │   │   │ - 000060_camerai.png (i-th Camera),
│   │   │   │ - 000060.pcd (LiDAR),
│   │   │   │ - 000060_radar.pcd (4D radar),
│   │   │   │ - 000060_fog.pcd (LiDAR with fog simulation),
│   │   │   │ - 000060_snow.pcd (LiDAR with snow simulation),
│   │   │   │ - 000060.yaml (LiDAR with fog simulation)
├── validate
│   ├──...
├── test
│   ├──...

```

### Calibration
We provide calibration information for each sensor (LiDAR, 4D radar, camera) of each agent for inter-sensor fusion. In particular, the exported 4D radar point cloud has been converted to the LiDAR coordinate system of the corresponding agent in advance of fusion, so the 4D radar point cloud is referenced to the LiDAR coordinate system.


## :balloon: Benchmark and Models Zoo
All benchmark model downloads require a login (using the username "Guest" and the password "guest_CMD")
### 4DRadar-based Cooperative 3D Detector (no-compression)
| **Method** | **Validation (IoU=0.3/0.5/0.7)** | **Testing (IoU=0.3/0.5/0.7)** | **Config** |  **Model**  |
|:--------------------------------:|:--------------------------------:|:-----------------------------:|:----------:|:-----------:|
|         [ITSC2021:PFA-Net](https://ieeexplore.ieee.org/abstract/document/9564754)         |         76.90/68.00/39.30        |       85.10/79.90/52.50       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_PFA_net.yaml)     | [model-25M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|           [NIPS2022:RTNH](https://arxiv.org/abs/2206.08171)          |         71.70/62.20/34.40        |       73.70/67.70/41.90       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_RTNH.yaml)     | [model-64M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|          [ECCV2022:V2XViT](https://arxiv.org/abs/2203.10638)         |         71.14/64.28/31.12        |       80.94/73.82/42.73       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_v2xvit.yaml)     | [model-51M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|         [ICRA2022:AttFuse](https://arxiv.org/abs/2109.07644)         |         75.30/66.50/36.10        |       81.80/75.40/48.20       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_attfuse.yaml)     | [model-25M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|        [NIPS2023:Where2comm](https://arxiv.org/abs/2209.12836)       |         71.60/67.20/42.90        |       80.40/77.30/56.70       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_where2comm.yaml)     | [model-30M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|          [ICCV2023:SCOPE](https://openaccess.thecvf.com/content/ICCV2023/html/Yang_Spatio-Temporal_Domain_Awareness_for_Multi-Agent_Collaborative_Perception_ICCV_2023_paper.html)          |         61.90/59.30/47.90        |       73.00/71.60/51.60       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_scope.yaml)     | [model-151M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|          [CoRL2023:CoBEVT](https://arxiv.org/abs/2207.02202)         |         80.20/73.40/41.10        |       85.80/80.60/52.90       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_cobevt.yaml)     | [model-40M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|         [ICRA2023:CoAlign](https://arxiv.org/abs/2211.07214)         |         65.80/59.20/34.70        |       76.90/70.20/46.20       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_coalign.yaml)     | [model-43M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|        [WACV2023:AdaFusion](https://openaccess.thecvf.com/content/WACV2023/html/Qiao_Adaptive_Feature_Fusion_for_Cooperative_Perception_Using_LiDAR_Point_Clouds_WACV_2023_paper.html)        |         77.84/72.48/42.85        |       82.20/78.08/55.51       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_adafusion.yaml)     | [model-27M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |
|           [IROS2024:SICP](https://arxiv.org/abs/2312.04822)          |         70.08/60.62/32.43        |       71.45/63.47/33.39       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/4DRadar/V2XR_sicp.yaml)     | [model-28M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/radar) |

### LiDAR-based Cooperative 3D Detector (no-compression)
| **Method** | **Validation (IoU=0.3/0.5/0.7)** | **Testing (IoU=0.3/0.5/0.7)** | **Config** |  **Model**  |
|:------------------------------:|:--------------------------------:|:-----------------------------:|:----------:|:-----------:|
| [ECCV2022:V2XViT](https://arxiv.org/abs/2203.10638)                | 84.99/82.22/64.92                | 90.14/89.01/77.71             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_v2xvit.yaml)     | [model-52M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [ICRA2022:AttFuse](https://arxiv.org/abs/2109.07644)               | 86.00/82.20/66.90                | 91.40/89.60/80.20             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_attfuse.yaml)     | [model-25M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [NIPS2023:Where2comm](https://arxiv.org/abs/2209.12836)         | 85.20/83.10/65.90                | 91.60/88.50/80.50             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_where2comm.yaml)     | [model-30M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [ICCV2023:SCOPE](https://openaccess.thecvf.com/content/ICCV2023/html/Yang_Spatio-Temporal_Domain_Awareness_for_Multi-Agent_Collaborative_Perception_ICCV_2023_paper.html)                 | 76.00/74.70/60.90                | 81.40/72.90/67.00             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_scope.yaml)     | [model-151M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [CoRL2023:CoBEVT](https://arxiv.org/abs/2207.02202)                | 87.64/84.79/71.01                | 92.29/91.44/82.45             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_cobevt.yaml)     | [model-40M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [ICRA2023:CoAlign](https://arxiv.org/abs/2211.07214)               | 89.08/87.57/80.05                | 89.59/88.89/83.29             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_coalign.yaml)     | [model-43M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [ICCV:AdaFusion](https://openaccess.thecvf.com/content/WACV2023/html/Qiao_Adaptive_Feature_Fusion_for_Cooperative_Perception_Using_LiDAR_Point_Clouds_WACV_2023_paper.html)                 | 88.11/86.91/75.61                | 92.70/90.60/84.80             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_adafusion.yaml)     | [model-27M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [IROS2024:SICP](https://arxiv.org/abs/2312.04822)                  | 81.14/77.62/58.14                | 84.64/82.17/66.71             |      [√](V2X-R/opencood/hypes_yaml/V2X-R/LiDAR/V2XR_sicp.yaml)     | [model-28M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |
| [WACV2024:MACP](https://openaccess.thecvf.com/content/WACV2024/html/Ma_MACP_Efficient_Model_Adaptation_for_Cooperative_Perception_WACV_2024_paper.html)                  | 72.80/70.90/60.00                | 83.70/83.10/75.50             |      soon     | [model-61M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/lidar) |


### LiDAR-4D Radar based Cooperative 3D Detector (no-compression)
|       **Method**       | **Validation (IoU=0.3/0.5/0.7)** | **Testing (IoU=0.3/0.5/0.7)** | **Config** |  **Model**  |
|:----------------------:|:--------------------------------:|:-----------------------------:|:----------:|:-----------:|
|  [IROS2023:InterFusion](https://ieeexplore.ieee.org/document/9982123)  |         81.23/77.33/52.93        |       87.91/86.51/69.63       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_InterFusion.yaml)     | [model-95M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|     [Arxiv2024:L4DR](https://arxiv.org/abs/2408.03677)     |         84.58/82.75/70.29        |       90.78/89.62/82.91       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_L4DR.yaml)     | [model-79M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|    [ICRA2022:AttFuse](https://arxiv.org/abs/2109.07644)    |         86.14/84.30/70.72        |       92.20/90.70/84.60       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_AttFuse.yaml)     | [model-95M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|     [ECCV2022:V2XViT](https://arxiv.org/abs/2203.10638)    |         85.23/83.90/69.77        |       91.99/91.22/83.04|[√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_V2XViT.yaml)     | [model-118M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|     [ICCV2023:Scope](https://openaccess.thecvf.com/content/ICCV2023/html/Yang_Spatio-Temporal_Domain_Awareness_for_Multi-Agent_Collaborative_Perception_ICCV_2023_paper.html)     |         78.79/77.96/62.57        |       83.38/82.89/70.00       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_Scope.yaml)     | [model-134M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
| [NIPS2023:Where2comm](https://arxiv.org/abs/2209.12836) |         87.62/85.58/69.61        |       92.20/91.00/82.04       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_where2comm.yaml)     | [model-30M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|     [CoRL2023:CoBEVT](https://arxiv.org/abs/2207.02202)    |         87.72/87.02/78.56        |       94.00/93.21/86.68       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_Cobevt.yaml)     | [model-40M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|    [ICRA2023:CoAlign](https://arxiv.org/abs/2211.07214)    |         87.16/85.54/73.79        |       92.14/91.21/83.77       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_CoAlign.yaml)     | [model-49M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|   [WACV2023:AdaFusion](https://openaccess.thecvf.com/content/WACV2023/html/Qiao_Adaptive_Feature_Fusion_for_Cooperative_Perception_Using_LiDAR_Point_Clouds_WACV_2023_paper.html)   |         89.24/87.31/74.86        |       93.10/92.15/85.48       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_AdaFusion.yaml)     | [model-27M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |
|      [IROS2024:SICP](https://arxiv.org/abs/2312.04822)     |         82.46/79.44/61.51        |       86.19/84.20/68.15       |      [√](V2X-R/opencood/hypes_yaml/V2X-R/L_4DR_Fusion/V2XR_Sicp.yaml)     | [model-28M](http://39.98.109.195:1000/files/V2X-R_Dataset(compressed)/benchmark/l+r) |

## :balloon: Quickly Get Started
Thanks to the contributions to [OpenCood](https://github.com/DerrickXuNu/OpenCOOD) and [BM2CP](https://github.com/byzhaoAI/BM2CP), this repository is proposed to be built on the basis of the two repositories mentioned above.

### Installation
Refer to [Installation of V2X-R](V2X-R/README.md)

