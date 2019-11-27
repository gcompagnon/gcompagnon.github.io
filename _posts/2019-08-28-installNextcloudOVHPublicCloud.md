---
layout: post
title: "Installation de NextCloud sur le Public Cloud d'OVH(french post)"
categories: notes nextcloud OVH
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [nextcloud, iaas, ovh]
excerpt: mode d emploi pour démarrer Nextcloud sur OVH
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

Les étapes pour déployer la solution de cloud fichier / coffre fort électronique en utilisant le serveur Nextcloud.

L'infrastructure Nextcloud ( version '16.0.4.1' adaptée avec la fonctionnalité /newpassword) est composée de :
  - un serveur Web (Nginx Debian9)
  - le serveur PHP (PHP7.3-FPM Debian9)
  - une instance de base de données (postgres 9.6 Debian9)
  - un volume de données fichiers (openstack swift)


Le déploiement de cette infrastructure peut se faire sur l'offre Public Cloud OVH:
  - un réseau privé pour isoler l'ensemble de cet infra
  - 1 instance pour le serveur Web et PHP
  - 1 instance distincte pour la base de données
  - 1 Object storage (dupliqués sur 3 réplicas et facturation à l'usage)
Le choix de séparer les instances de type Front (Web) et Back (Base de données) est dictée par un choix d'architecture pour faciliter la montée en charge (installation d'un load balancer en facade des instances Web, en conservant une instance dédiée pour le Back) , l'isolation des composants permettant l'archivage cohérent ( snapshot et cloud archive) un PCA (passage d'une région à une autre), et la sécurité avec si nécessaire un reverse proxy.


![alt text](/images/20190828-logo-coffre-fort.svg "CFE / ")


# A. Installation des prerequis

## 0- Créer sa clé SSH 

https://docs.ovh.com/fr/public-cloud/creation-des-cles-ssh/

Générer une paire de clés SSH (rsa longueur 4096)  (par exemple sur linux/debian)
```
ssh-keygen -t rsa -b 4096
```
(importer sur son système / par ex sur linux dans ~/.ssh)
importer la clé publique SSH dans la console OVH :
https://horizon.cloud.ovh.net/project/key_pairs

## 1- Réseaux privés / Private network

Créer un réseau privé avec une interface par défaut , et un VLAN id
Les IP du réseau sont gérés par DHCP par simplicité (pas besoin de fixer les IPs)

## 2- instance Postgresql 
Il existe un choix entre héberger la base de données sur une instance Compute ou alors utiliser une CloudDB (service DBaas d'OVH)
=> pour des raisons de flexibilité de coût et d'usage (CloudDB est au minimum une instance à 512Mo), le démarrage du projet se fera sur une instance Compute

Documentation sur les instances Cloud Public: [Premiers pas avec une instance Public Cloud](https://docs.ovh.com/fr/public-cloud/debuter-avec-une-instance-public-cloud/)


-renseigner l'adresse IP privée de postgres sans le DNS \<nom domaine>.com 
cela eviter de coder l adresse IP dans les fichiers de config
https://docs.ovh.com/fr/domains/editer-ma-zone-dns/
```
Modifier l'entrée A de postgres.\<domain>.com
```

-Se connecter en SSH sur l instance :

```
ssh debian@*ip publique*
```
https://docs.ovh.com/gb/en/public-cloud/first-login/#instructions

pour activer VNC, changer le mot de passe root
https://docs.ovh.com/fr/public-cloud/passer-root-et-definir-un-mot-de-passe/
```
 sudo passwd 
```

- installer Postgresql

```
sudo apt-get install postgresql
```

- creer l utilisateur et l'instance de base de données: nextcloud

```
sudo -u postgres psql -c "SELECT version();"
sudo -u postgres  createuser -P nextcloud

sudo -u postgres psql -d template1
psql (9.6.13)
Type "help" for help.

template1=# ALTER USER nextcloud CREATEDB;
template1=# CREATE DATABASE nextcloud16 OWNER nextcloud;
template1=# GRANT ALL PRIVILEGES ON DATABASE nextcloud16 to nextcloud;


WARNING:  could not flush dirty data: Function not implemented
CREATE DATABASE
template1=# \q
```

- activer l ecoute sur l interface privé du serveur

```
sudo -u postgres vi /etc/postgresql/9.6/main/postgresql.conf
```

et modifier la ligne #listen_addresses = 'localhost' en
listen_addresses = 'postgres.\<domain>.com'

- permettre les connexions provenant de la plage des adresses privées

```
sudo vi /etc/postgresql/9.6/main/pg_hba.conf
```
ajouter la ligne :
```
host    all             all             10.72.1.0/24            md5 
```

- configurer le serveur en fonction de la taille du serveur
par exemple pour un serveur 2 cores et 7Go de RAM
```
shared_buffers = 1792MB  (1/4 de la RAM)
work_mem = 18MB  (1/4 de la RAM / nb de connections simulatée)
```
- redemarrer postgres

```
sudo service postgres restart
```

- faire en sorte que postgres redémarre à chaque reboot d instance

```
sudo update-rc.d postgresql enable
```

## 3- Object Storage

https://docs.ovh.com/gb/en/public-cloud/place-an-object-storage-container-behind-domain-name/

créer un Object storage XXXXX en mode private / non-public

conserver le mot de passe et le fichier openRC

## 4 - Instance serveur Front pour servir NextCloud

- créer et démarrer une instance Compute / Debian9

-renseigner l'adresse IP PUBLIC de cette instance sans le DNS \<domaine>.com
https://docs.ovh.com/fr/domains/editer-ma-zone-dns/
```
Modifier l'entrée A de coffre.<domaine>.com avec l adresse IP v4 Public
Modifier l'entrée AAAA de coffre.<domaine>.com avec l adresse IP v6 Public
```

-Se connecter en SSH sur l instance :

```
ssh debian@*ip publique*
```

- changer le mot de passe root

```
 sudo passwd 
```

- ajouter un user nextcloud

```
sudo adduser  nextcloud

usermod -aG sudo nextcloud

su - nextcloud
```
- install un point de montage sur l'object storage
https://github.com/ovh/svfs/blob/master/docs/PCS.md


- installer le package OVH pour supporter Swift

```
sudo apt-get install ruby fuse

 wget https://github.com/ovh/svfs/releases/download/v0.9.1/svfs_0.9.1_amd64.deb .

 sudo dpkg -i svfs_0.9.1_amd64.deb
```

- utiliser les variables décrites dans le fichier OpenRC openrc.sh
https://docs.ovh.com/fr/public-cloud/acces-et-securite-dans-horizon/

et choisir un \<nom> 
```
sudo mkdir /mnt/<nom>
sudo chown nextcloud:nextcloud /mnt/<nom>

sudo mount -t svfs -o username=$OS_USERNAME,password=$OS_PASSWORD,tenant=$OS_TENANT_NAME,region=$OS_REGION_NAME,uid=nextcloud,gid=nextcloud <nom> /mnt/<nom>
```

## 5- NGINX

sur l'instance Serveur Front / Nextcloud
```
sudo apt-get install nginx
```

- utiliser le user nextcloud pour démarrer nginx

```
sudo vi /etc/nginx/nginx.conf
```
changer *user www-data;* en 
```
user nextcloud;
```

- installer la configuration Nextcloud pour nginx

```
cp nextcloud.conf /etc/nginx/sites-enabled/
```

## 6- NGINX avec HTTPS

Lets encrypt fournit un certificat SSL au serveur NGINX

- ajouter un referentiel pour obtenir le certbot qui renouvelera le certificat SSL 

```
sudo apt edit-sources
```
et ajouter les 2 lignes (ou décommentez du fichier sources.list):
deb http://deb.debian.org/debian stretch-backports main contrib non-free
deb-src http://deb.debian.org/debian stretch-backports main contrib non-free
```
sudo apt update
sudo apt install python-certbot-nginx -t stretch-backports
```

- obtenir un certificat SSL pour notre instance

```
sudo certbot --nginx -d coffre.xxxxx.com -d www.coffre.xxxxx.com
```
## 7 -php7.3-fpm
sur l'instance Serveur Front / Nextcloud

- ajouter la source officielle pour les packages PHP

```
sudo apt -y install lsb-release apt-transport-https ca-certificates 

sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php7.3.list
```

- installer PHP 7.3

```
 sudo apt update
 sudo apt upgrade -y
 sudo apt -y install php7.3
```

- installer les modules complementaires PHP 7.3

```
sudo apt install php7.3-cli php7.3-fpm php7.3-json php7.3-pdo php7.3-zip php7.3-gd  php7.3-mbstring php7.3-curl php7.3-xml php7.3-json

 sudo apt install php7.3-pgsql php7.3-apc php7.3-apcu php7.3-imagick php7.3-imap php7.3-intl imagemagick
```
- installer le module Imagemagick pour gérer les logos custom

```
sudo apt-get install build-essential checkinstall && apt-get build-dep imagemagick -y
sudo apt-get -y install gcc make autoconf libc-dev pkg-config
sudo apt-get -y install libmagickwand-dev
sudo apt update
```

- utiliser le user nextcloud pour démarrer php

```
sudo vi /etc/php/7.3/fpm/pool.d/www.conf  
```
changer *www-data* en 
```
listen.owner = nextcloud
listen.group = nextcloud 
```

changer 
```
;clear_env = no
en 
clear_env = no
```

- augmenter la mémoire allouée pour PHP

```
sudo vi /etc/php/7.3/fpm/php.ini  
```
changer memory_limit = 128M en 
```
memory_limit = 512M
```


- augmenter le nombre de process allouées au traitement PHP
(dans le cas où le serveur hébergeant le service "nextcloud" est un b2-7 / 2 vCores 7Go de RAM)

```
sudo vi /etc/php/7.3/fpm/pool.d/www.conf
```

```
pm.max_children = 70
pm.start_servers = 20
pm.min_spare_servers = 10
pm.max_spare_servers = 35
```


- faire en sorte que php7.3-fpm et nginx redémarre à chaque reboot d instance

```
sudo update-rc.d php7.3-fpm enable
sudo update-rc.d nginx enable
```



# B. Installation de Nextcloud
en version 16.0.4 / stable

[documentation officielle Nextcloud16](https://docs.nextcloud.com/server/16/admin_manual/installation/index.html)


## 0- Choix de la zone de stockage

Il existe de multiples possibilités pour représenter la zone de stockage des fichiers accessibles par NextCloud.

-espace disque en local du serveur nextcloud
-point de montage réseau sur un SAN/NAS
-technologie de Cloud Storage (Amazon S3, Openstack Swift)

OVH Public Cloud propose les conteneurs d'objet basé sur le standard Openstack Swift à tarif compétitif. ce qui assure une sureté de fonctionnement (3 replicas de la donnée), et la facilité pour la sauvegarde, l'archivage.

### Nextcloud et Openstack Swift

Il existe 3 façons d'utiliser un conteneur d'objects Swift:

#### 1. en Primary Storage :
Le primary storage est configuré directement dans Nextcloud config.php
https://docs.nextcloud.com/server/15/admin_manual/configuration_files/primary_storage.html
De façon à rendre l'accès exclusif à l'instance Nextcloud.
C'est pourquoi, il faut s'assurer de sauvegarder la base de données Nextcloud , contenant les meta données, 
ainsi que la clé de chiffrement serveur permettant de déchiffrer à la volée les éléments/objets stockés dans le conteneur Swift

#### 2. en dossier dans nextcloud par l 'application "external storage support"

- installer la librairie pour accèder à Openstack Swift

```
sudo apt-get install smbclient
```

- utiliser les variables décrites dans le fichier OpenRC openrc.sh

- Aller dans Paramètres (icone haut et droit) et dans "Stockages externes"

creer un dossier 
Nom du dossier : OVHPublicCloud
Stockage externe: Openstack Object STorage

Nom du service : swift
Région: \$OS_REGION_NAME
Bucket: \<nom>_01 (le nom d un des conteneurs objectstorage OVH)
Nom d utilisateur: \$OS_USERNAME
Mot de passe: \$OS_PASSWORD
Tenant name: \$OS_TENANT_NAME
Identity endpoint URL: https://auth.cloud.ovh.net/v2.0/

#### 3. en point de montage réseau avec OVH/svfs
https://github.com/ovh/svfs


## 1- installation de nextcloud
La version adaptée de nextcloud (avec /newpassword ,  Aide A la découverte / firstrunwizard et Customized Emails est disponible sur l'object storage

```
tar xvf nextcloud-server-<nom>.tar.gz
ou (partir d'une version officielle de nextcloud.org)
tar xvf nextcloud-n.x.y.tar.bz2
```

## 2- (si nécessaire) installation pour ré-initialiser Nextcloud 

Afin de démarrer un serveur avec le seul compte admin, sans tenir compte de la base de données existante.

 - créer un base de données vide , avec psql : 

```
sudo -u postgres psql -d template1
psql (9.6.13)
Type "help" for help.

template1=# CREATE DATABASE nextcloud16 OWNER nextcloud;
```

- retirer ou renommer le fichier config/config.php
- créer un fichier vide config/CAN_INSTALL

Solliciter Nextcloud sur http://serveur_IP:Port/ afin de d'obtenir la page d'installation


## 3- (pour info) Fichier de configuration

Le fichier de configuration principal est config/config.php 

## 4- Applications

APrès une connexion avec le compte admin de nextcloud 
Aller dans Applications (icone haut et droit)
pour activer:

Aide A la Decouverte (FirstRunWizard adaptée)
Theming
Custom CSS 
Customized Emails 
Default encryption module 

### Theming & Custom CSS 
Aller dans Paramètres (icone haut et droit) et dans "Personnaliser l'apparence"

Choisir les logos présents dans /nextcloud-server/themes/xxx.core/img
et le contenui du fichier /nextcloud-server/themes/xxx.core/css/custom.css dans Custom CSS et cliquer sur Save

### Securité:
Aller dans Paramètres (icone haut et droit) et dans "Sécurité"
Activer le chiffrement coté serveur
et
Chiffrer l'espace de stockage principal

### Bruteforce / protection contre l'attaque par force brute

Aller dans Paramètres (icone haut et droit) et dans "Sécurité"
Ajouter l'adresse IP Publique de Sagis dans Liste blanche des IP pour attaque par force brute:
 193.248.39.218


# C. Configuration des tâches de fond

 Dans la console d'administration, dans les Paramètres de base, il faut choisir le système en charge d'exécuter les travaux en arrière plan.

 Par défaut, AJAX est choisi mais le paramètre recommandé est 'cron'

Une explicatin précise est disponible [ici](https://docs.nextcloud.com/server/16/admin_manual/configuration_server/background_jobs_configuration.html#cron)

```
crontab -u nextcloud -e
```
Ajouter cette ligne
```
 */5  *  *  *  * php -f /home/nextcloud/nextcloud-server/cron.php
```
CTRL^X

```
nextcloud@nextcloud-prod:~$ crontab -u nextcloud -l
```

# D. Réinitialisation de Nextcloud

Pour démarrer un serveur Nextcloud avec une base de données réinitialisées , et aucun document enregistré:

```
sudo vi nextcloud-server/config/config.php

```
remplacer la ligne:
```
'installed' => true,
```
par
```
'installed' => false,
```

*et*

creer un fichier vide dans le répertoire config
```
sudo vi nextcloud-server/config/CAN_INSTALL
```

# E. Environnement par défaut à la création d'un compte Client

Le répertoire par défaut est défini par le theming (défini dans config.php):
```
ls nextcloud-server/themes/sagis/core/skeleton

Personnel

```
https://docs.nextcloud.com/server/16/admin_manual/configuration_files/default_files_configuration.html