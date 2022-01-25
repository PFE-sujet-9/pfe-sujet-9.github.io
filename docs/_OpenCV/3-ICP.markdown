---
layout: default
title:  "Algorithme ICP"
categories: icp algorithm
---

# Prérequis

Pour visualiser des nuages de points, il faut le module Viz de OpenCV. Un tuto est disponible [ici](/OpenCV/1-installation).

# Lecture des nuages

```
source = cv.viz.readCloud("Datasets/pointscloud_000.ply")
target = cv.viz.readCloud("Datasets/pointscloud_100.ply")
```

Les nuages de points sont de la formes suivantes : [[positions], [couleurs]].

> Il est possible que vous ayez des difficultés à lire les nuages de points. Pour cela, consultez [ceci](/OpenCV/2-Viz).

# Estimation des normales

Pour améliorer la qualité du recalage, on peut estimer les normales de chaque point. Pour cela, on va utiliser [`cv.ppf_match_3d.computeNormalsPC3d`](https://docs.opencv.org/4.x/d9/d25/group__surface__matching.html#gaa74e62c7b2a8b8a70e44e42211fd00f5). `computeNormalsPC3d` prend 4 paramètres : 
* le nuage de points, 
* le nombre de voisins pris en compte pour le calcul de la normale, 
* un booléen pour savoir si les normales doivent être dans un sens particulier 
* le point de vue pour le sens des normales.

Nous allons estimer les normales que sur les positions des points, c'est pour ca que nous allons prendre l'élément [0][0] des nuages de points.

```
retval, source_normals = cv.ppf_match_3d.computeNormalsPC3d(source[0][0], 30, False, (0, 0, 0))
retval2, targets_normals = cv.ppf_match_3d.computeNormalsPC3d(target[0][0], 30, False, (0, 0, 0))
```

# Echantillonnage

Pour augmenter la rapidité et la possibilité de convergence, on va échantilloner le nuage de points. On va utiliser [`cv.ppf_match_3d.samplePCByQuantization`](https://docs.opencv.org/4.x/d9/d25/group__surface__matching.html#ga685d0fc50635dfdd4ebc587067ff44a9). Cette fonction prend 6 paramètres :
* le nuage de points,
* x<sub>min</sub> et x<sub>max</sub>,
* y<sub>min</sub> et y<sub>max</sub>,
* z<sub>min</sub> et z<sub>max</sub>,
* un pas d'échantillognage,
* un entier pour donner plus de poids aux points situés proche de l'origine.

## Exemple

```
import cv2 as cv
import numpy as np

pcd = cv.viz.readCloud("Datasets/pointscloud_000.ply")

min_x = 0
max_x = 0
min_y = 0
max_y = 0
min_z = 0
max_z = 0

for ligne in pcd[0][0]:
    if(ligne[0] > max_x):
        max_x = float(ligne[0])
    if(ligne[0] < min_x):
        min_x = float(ligne[0])
    if(ligne[1] > max_y):
        max_y = float(ligne[1])
    if(ligne[1] < min_y):
        min_y = float(ligne[1])
    if(ligne[2] > max_z):
        max_z = float(ligne[2])
    if(ligne[2] < min_z):
        min_z = float(ligne[2])

pcd_sample = cv.ppf_match_3d.samplePCByQuantization(pcd[0][0], (min_x, max_x), (min_y, max_y), (min_z, max_z), 0.01)

viewer = cv.viz_Viz3d("Nuages de points")


source = cv.viz_WCloud(pcd[0])
viewer.showWidget("Source", source)

cloud_pose = cv.viz_Affine3d()
cloud_pose = cloud_pose.translate((40, 0, 0))

source_sample = cv.viz_WCloud(np.array([np.array(pcd_sample)]))
viewer.showWidget("Source_sample", source_sample, cloud_pose)

viewer.spin()

```

Résultat :

![Sampling](../../assets/Open3D/ICP/Sampling.png)

# Recalage

Pour le recalage, nous allons utiliser [`cv.ppf_match_3d_ICP`](https://docs.opencv.org/4.x/dc/d9b/classcv_1_1ppf__match__3d_1_1ICP.html). 
[`	cv.ppf_match_3d_ICP`](https://docs.opencv.org/4.x/dc/d9b/classcv_1_1ppf__match__3d_1_1ICP.html#af0a4a96762f10658963bbfa5f84c3091) prend 4 paramètres :
* le nombre d'itérations,
* la tolérance,
* la valeur de l'écart-type minimum accepté, cela permet d'éliminer les valeurs aberrantes,
* le nombre de niveau de pyramide à explorer. Des pyramides profondes sont plus rapides mais moins précises (généralement entre 4 et 10).

```
import cv2 as cv
import numpy as np

source = cv.viz.readCloud("Datasets/pointscloud_000.ply")
target = cv.viz.readCloud("Datasets/pointscloud_100.ply")

retval, source_normals = cv.ppf_match_3d.computeNormalsPC3d(source[0][0], 30, False, (0, 0, 0))
retval2, target_normals = cv.ppf_match_3d.computeNormalsPC3d(target[0][0], 30, False, (0, 0, 0))


min_x = 0
max_x = 0
min_y = 0
max_y = 0
min_z = 0
max_z = 0

for ligne in source_normals:
    if(ligne[0] > max_x):
        max_x = float(ligne[0])
    if(ligne[0] < min_x):
        min_x = float(ligne[0])
    if(ligne[1] > max_y):
        max_y = float(ligne[1])
    if(ligne[1] < min_y):
        min_y = float(ligne[1])
    if(ligne[2] > max_z):
        max_z = float(ligne[2])
    if(ligne[2] < min_z):
        min_z = float(ligne[2])

source_sample = cv.ppf_match_3d.samplePCByQuantization(source_normals, (min_x, max_x), (min_y, max_y), (min_z, max_z), 0.005, 2)

min_x = 0
max_x = 0
min_y = 0
max_y = 0
min_z = 0
max_z = 0

for ligne in target_normals:
    if(ligne[0] > max_x):
        max_x = float(ligne[0])
    if(ligne[0] < min_x):
        min_x = float(ligne[0])
    if(ligne[1] > max_y):
        max_y = float(ligne[1])
    if(ligne[1] < min_y):
        min_y = float(ligne[1])
    if(ligne[2] > max_z):
        max_z = float(ligne[2])
    if(ligne[2] < min_z):
        min_z = float(ligne[2])

target_sample = cv.ppf_match_3d.samplePCByQuantization(target_normals, (min_x, max_x), (min_y, max_y), (min_z, max_z), 0.005, 2)

icp = cv.ppf_match_3d_ICP(1000000, 0.001, 1.5, 4)
result = icp.registerModelToScene(source_sample, target_sample)


res = cv.ppf_match_3d.transformPCPose(source[0][0], result[2])

source[0][0] = res

viewer = cv.viz_Viz3d("Nuages de points")

axe = cv.viz_WCoordinateSystem()
viewer.showWidget("axe",axe)

source = cv.viz_WCloud(source[0], source[1])
viewer.showWidget("Source", source)

target = cv.viz_WCloud(target[0], target[1])
viewer.showWidget("Target", target)

viewer.spin()

```
 
Résultat :
![Registration](../../assets/Open3D/ICP/Registration.png)

Valeur de result :
```
(0, 0.005591467522456486, array([[ 0.99207945,  0.01622667, -0.12455947, -2.78593678],
     				  [ 0.01647234,  0.96625151,  0.25707332, 11.44790077],
     				  [ 0.12452722, -0.25708894,  0.95833097,  4.23495627],
     				  [ 0.        ,  0.        ,  0.        ,  1.        ]]))
```

