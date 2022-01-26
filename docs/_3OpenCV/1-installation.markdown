---
layout: default
title:  "Installation d'OpenCV"
categories: opencv install
---

Pour pouvoir installer OpenCV avec le module [Viz](https://docs.opencv.org/3.4/d9/d62/namespacecv_1_1viz.html), il va falloir le compiler avec VTK.

## Prérequis

Avant toute chose, il faudra installer [Python](https://www.python.org/downloads/), [CMake](https://cmake.org/download/) et [VTK](https://vtk.org/download/).

### Installer Python

#### Ubuntu

```
sudo apt-get install python3
```
Pour vérifier la bonne installation, tapez `python3 -V` dans un terminal.

### Installer Cmake

#### Ubuntu

```
sudo apt-get install cmake
```
Pour vérifier la bonne installation, tapez `cmake /V` dans un terminal.

### Installation de VTK

#### Ubuntu

```
sudo apt-get install libvtk7-dev
```
Pour vérifier la bonne installation, tapez `vtk7` dans un terminal. Si une fenêtre s'ouvre, c'est bon.

## Installer OpenCV

Il faut d'abord cloner le dépot de opencv et de opencv_contrib.

> opencv_contrib regroupe des modules supplémentaires qui ne sont pas dans la version d'OpenCV de base.

```
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```

Maintenant, placez-vous dans le dossier d'OpenCV et créez un dossier build :

```
mkdir build
cd build
```

Mainteant, executez :
```
ccmake ..
```

Une fenre va s'ouvrir. Tapez sur la touche `c` de votre clavier.
Une fois la configuration terminée, vous allez avoir une liste d'options.

Il faut que les options suivantes soient configurées:

```
WITH_VTK = ON
OPENCV_EXTRA_MODULES = /path/to/opencv_contrib/directory/modules
```

Veillez à ce que CMake trouve tout seul `PYTHON3_EXECUTABLE` et `VTK_DIR`.
Une fois ces options bien configurés, vous pouvez générer en tapant sur la touche `g` de votre clavier.

> Si [g] n'est pas dans les options en bas de la fenêtre, faites [c] jusqu'à ce que l'option apparaisse.

Maintenant on va pouvoir compiler :
```
make
sudo make install
```

Trouver le dossier cv2. Pour ma part, il était à l'emplacement `/usr/local/lib/python3.8/site-packages`.
Ajoutez cet emplacement dans le ~/.bashrc :
```
nano .bashrc
```
puis ajouter a la fin :
```
export PYTHONPATH=/usr/local/lib/python3.8/site-packages:$PYTHONPATH
```

Ouvrez un nouveau terminal et faites :
```
python3
import cv2
cv2.viz 
```

Si vous obtenez `<module 'cv2.viz'>`, c'est bon !
