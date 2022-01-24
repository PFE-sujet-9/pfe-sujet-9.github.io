---
layout: default
title:  "Visualiser un nuage de points"
categories: viz opencv
---

# Prérequis

Pour visualiser des nuages de points, il faut le module Viz de OpenCV. Un tuto est disponible [ici](/OpenCV/1-installation)

# Code 

Voici le code pour visualiser un nuage de points avec OpenCV.

```
import cv2 as cv

pcd = cv.viz.readCloud("Datasets/pointscloud_000.ply")

viewer = cv.viz_Viz3d("Nuages de points") # Création du viewer

axe = cv.viz_WCoordinateSystem()
viewer.showWidget("axe",axe)

pcd_widget = cv.viz_WCloud(pcd[0], pcd[1]) 
#pcd[0] correspond aux points et pcd[1] aux couleurs

viewer.showWidget("PointsCloud", pcd_widgets)

viewer.spin()
```

# Quelques problèmes rencontrés

## Problème lors de la lecture des nuages de points

Les fichiers du dataset n'arrivaient pas à être lus par OpenCV. En effet, les données sont encodées. Quand on ouvre le .ply avec un éditeur de texte, on observe plein de caractères bizarres.
Pour corriger cela, il  faut télécharger [Meshlab](https://www.meshlab.net/), puis **Import Mesh ...**, et enfin **Export Mesh ...** en décochant *Binary encoding*.
