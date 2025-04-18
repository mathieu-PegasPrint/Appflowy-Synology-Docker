# Appflowy-Synology-Docker
Installer Appflowy sur un Nas Synology compatible Docker / Container Manager

# Prérequis

1. Avoir un Nas synology compatible Container Manager :
https://www.synology.com/fr-fr/products?product_line=ds_j%2Cds_plus%2Cds_value%2Cds_xs&pkg_support=VMM
2. Installer Container Manager
3. Installer Git Server
4. Option installer Editeur de texte
  # Installation
  
1. Creer le dossier docker à la racine de preference si ce n'est pas deja fait
2. Creer une tache en utilisateur root en script utilisateur
```
#!/bin/bash
cd /volume1/docker
git clone https://github.com/AppFlowy-IO/AppFlowy-Cloud.git
```
Demarrer la tache et attendre un peux le temps que tout le projet AppFlowy-Cloud soit copié, une fois fait un dossier va apparaitre dans docker "AppFlowy-Cloud"
3. Copier ce fichier Nginx.conf dans le dossier /volume1/docker/AppFlowy-Cloud/nginx/
