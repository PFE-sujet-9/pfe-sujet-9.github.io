---
layout: default
title:  "Algorithme ICP"
categories: icp algorithm
---

# Prérequis

Il faut installer Open3D comme expliqué [ici](/Open3D/2-ICP).

# Lecture des nuages de points

```
import open3d as o3d

# On importe les deux fichiers
source = o3d.io.read_point_cloud("Datasets/pointscloud_000.ply")
target = o3d.io.read_point_cloud("Datasets/pointscloud_100.ply")
```

![GIF orginal pointsclouds](../assets/Open3D/ICP/original.gif)

# Estimation des normales

```
# On estime les normales
# radius = rayon de recherche des voisins et max_nn = nombre de voisins
source.estimate_normals(search_param=o3d.geometry.KDTreeSearchParamHybrid(radius=1, max_nn=30))
target.estimate_normals(search_param=o3d.geometry.KDTreeSearchParamHybrid(radius=1, max_nn=30))
```

![GIF normals pointsclouds](../assets/Open3D/ICP/normals.gif)

# Echantillonnage

```
# On choisit un point par voxel de taille 1
voxel_size = 1
source_down = source_copy.voxel_down_sample(voxel_size=voxel_size)
target_down = target_copy.voxel_down_sample(voxel_size=voxel_size)
```

![GIF sampling pointsclouds](../assets/Open3D/ICP/sampling.gif)

# Recalage

```
# Le but de ce programme est de montrer un exemple de comment paramétrer les différentes fonctions

import open3d as o3d
import numpy as np
import copy

# On importe les deux fichiers
source = o3d.io.read_point_cloud("Datasets/pointscloud_000.ply")
target = o3d.io.read_point_cloud("Datasets/pointscloud_100.ply")

source_copy = copy.deepcopy(source)
target_copy = copy.deepcopy(target)

# On estime les normales
# radius = rayon de recherche des voisins et max_nn = nombre de voisins
source_copy.estimate_normals(search_param=o3d.geometry.KDTreeSearchParamHybrid(radius=1, max_nn=30)) 
target_copy.estimate_normals(search_param=o3d.geometry.KDTreeSearchParamHybrid(radius=1, max_nn=30))

# On choisit un point par voxel de taille 1
voxel_size = 1
source_down = source_copy.voxel_down_sample(voxel_size=voxel_size)
target_down = target_copy.voxel_down_sample(voxel_size=voxel_size)


# L'alignement initial est généralement obtenu par un algorithme de recalage global.
current_transformation = np.identity(4)

# Si le chevauchement est plus grand que relative_fitness, alors le programme s'arrête
# Si l'erreur quadratique moyenne est plus petite que relative_rmse, alors le programme s'arrête
# On définit un nombre d'itération maximale
result_icp = o3d.pipelines.registration.registration_colored_icp(
        source_down, target_down, voxel_size, current_transformation,
        o3d.pipelines.registration.TransformationEstimationForColoredICP(),
        o3d.pipelines.registration.ICPConvergenceCriteria(relative_fitness=1e-1,
                                                          relative_rmse=1e-6,
                                                          max_iteration=2000))

# fitness mesure la zone de chevauchement = nombre de correspondances / nombre de points de la source. Plus c'est grand, mieux c'est.
# inliner_rmse mesure l'erreur quadratique moyenne. Plus elle est petite, mieux c'est.

def draw_registration_result_original_color(source, target, transformation):
    source_temp = copy.deepcopy(source)
    source_temp.transform(transformation)
    o3d.visualization.draw_geometries([source_temp, target])

draw_registration_result_original_color(source, target, result_icp.transformation)
```

![GIF registration pointsclouds](../assets/Open3D/ICP/registration.gif)

Valeur de result_icp :
```
RegistrationResult with fitness=5.994593e-01, inlier_rmse=5.164453e-01, and correspondence_set size of 1552
```

```
[[ 9.90999579e-01  2.28145206e-02 -1.31906526e-01 -3.14435479e+00]
 [ 1.10210434e-02  9.68119646e-01  2.50245656e-01  1.10904299e+01]
 [ 1.33410533e-01 -2.49447087e-01  9.59154722e-01  4.34814387e+00]
 [ 0.00000000e+00  0.00000000e+00  0.00000000e+00  1.00000000e+00]]
```

![GIF animation pointsclouds](../assets/Open3D/ICP/animation.gif)

# Bibliographie

[Documentation Open3D](http://www.open3d.org/docs/release/tutorial/pipelines/icp_registration.html)
