---
layout: default
title:  "Sujet"
categories: subject
---

# Recalage de nuages de points 3D

Ce projet porte sur la mise en correspondance deux (ou plusieurs) nuages de points 3D en utilisant l’information commune (caractérisation, dans chacun des nuages, des sous-nuages qui doivent se correspondre, et mise en correspondance géométrique) ; plusieurs approches ont été développées à cet effet, de type ICP (Iterated Closest Point), RANSAC (Random Sample Consensus) ou Transformée de Hough ; il reste néanmoins un verrou scientifique à résoudre dans le cadre de notre approche lié au fait que les nuages de points sont acquis à un facteur d’échelle près (en effet, la distance à l’objet n’est pas constante) alors que, le plus souvent, dans les approches actuelles, la transformation de recalage recherchée est une isométrie.

Technologies : Open3D, OpenCV

## Références :

* X. Huang, G. Mei, J. Zhang, R. Abbas, A comprehensive survey on point cloud registration, in CVPR 2021. 
* J. Park, Q.-Y. Zhou, and V. Koltun, Colored Point Cloud Registration Revisited, ICCV, 2017. 
* S. Choi, Q.-Y. Zhou, and V. Koltun, Robust Reconstruction of Indoor Scenes, CVPR, 2015.
* F. Steinbrucker, J. Sturm, and D. Cremers, Real-time visual odometry from dense RGB-D images, In ICCV Workshops, 2011. 
* S. Rusinkiewicz and M. Levoy. Efficient variants of the ICP algorithm. In 3-D Digital Imaging and Modeling, 2001. 
* Y. Chen and G. G. Medioni, Object modelling by registration of multiple range images, Image and Vision Computing, 10(3), 1992. 
* P. J. Besl and N. D. McKay, A Method for Registration of 3D Shapes, PAMI, 1992. 