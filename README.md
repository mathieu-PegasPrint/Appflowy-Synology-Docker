# Appflowy-Synology-Docker
Installer Appflowy sur un Nas Synology compatible Docker / Container Manager

# Prérequis

1. Avoir un Nas synology compatible Container Manager :
https://www.synology.com/fr-fr/products?product_line=ds_j%2Cds_plus%2Cds_value%2Cds_xs&pkg_support=VMM
2. Installer Container Manager
3. Installer Git Server
4. Option installer Editeur de texte
5. Si vous souhaitez utiliser l'application en dehors de chez vous, il faut utiliser un nom de domaine avec un proxy inversé (celui du nas fait très bien l'affaire)
Des tutoriels sont disponible pour creer un proxy inversé
  # Installation 
  
1. Creer le dossier docker à la racine de preference si ce n'est pas deja fait
2. Creer une tache en utilisateur root en script utilisateur
```
#!/bin/bash
cd /volume1/docker
git clone https://github.com/AppFlowy-IO/AppFlowy-Cloud.git
```
Demarrer la tache et attendre un peux le temps que tout le projet AppFlowy-Cloud soit copié, une fois fait un dossier va apparaitre dans docker "AppFlowy-Cloud"

3. Copier le fichier Nginx.conf du projet dans le dossier /volume1/docker/AppFlowy-Cloud/nginx/

Le changement effectué est en ligne 38 et 39, j'ai changé le port d'écoute 80 et 443 en 8090 afin de ne pas avoir de conflit avec le Nas Synology
```
#listen 80;
#listen 443 ssl;
listen 8090;
```
4. On créer 2 dossiers dans /volume1/docker/AppFlowy-Cloud
 - minio_data
 - postgres_data
5. On modifie le fichier deploy.env qu'on copie en fichier .env
6. On modifie le fichier docker-compose.yml
   
   Les changements :
   - service nginx port d'écoute
    ports:
      - 8090:8090
   - Montage des dossiers minio_data et postgres_data
```
minio:
    restart: on-failure
    image: minio/minio
    environment:
      - MINIO_BROWSER_REDIRECT_URL=${APPFLOWY_BASE_URL?:err}/minio
      - MINIO_ROOT_USER=${APPFLOWY_S3_ACCESS_KEY:-minioadmin}
      - MINIO_ROOT_PASSWORD=${APPFLOWY_S3_SECRET_KEY:-minioadmin}
    command: server /data --console-address ":9001"
    volumes:
      - /volume1/docker/AppFlowy-Cloud/minio_data:/data

  postgres:
    restart: on-failure
    image: pgvector/pgvector:pg16
    environment:
      - POSTGRES_USER=${POSTGRES_USER:-postgres}
      - POSTGRES_DB=${POSTGRES_DB:-postgres}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD:-password}
      - POSTGRES_HOST=${POSTGRES_HOST:-postgres}
      - SUPABASE_PASSWORD=${SUPABASE_PASSWORD:-root}
    healthcheck:
      test: [ "CMD", "pg_isready", "-U", "${POSTGRES_USER}", "-d", "${POSTGRES_DB}" ]
      interval: 5s
      timeout: 5s
      retries: 12
    volumes:
      - ./migrations/before:/docker-entrypoint-initdb.d
      - /volume1/docker/AppFlowy-Cloud/postgres_data:/var/lib/postgresql/data
```
7. On creer une nouvelle tache en script utilisateur root
```
#!/bin/bash
cd /volume1/docker/AppFlowy-Cloud
docker-compose pull && docker-compose up -d
```
Voilà votre application est en route http://IP_NAS:8090
