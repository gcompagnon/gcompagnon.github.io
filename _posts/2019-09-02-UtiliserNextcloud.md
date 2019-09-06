---
layout: post
title: "Nextcloud au quotidien / admininstration applicative(french post)"
categories: notes nextcloud OVH
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [nextcloud, iaas, ovh]
---

Ce document décrit quelques actions systématiques d'administration afin de faire évoluer l'usage du coffre-fort en ligne / cloud fichiers /extranet clients

![alt text](/images/20190828-logo-coffre-fort.svg "CFE / ")


Le serveur Nextcloud propose d'utiliser une interface graphique pour l'administrateur:
- https://coffre.\<nom>.com/settings/users
ou
- un utilitaire en ligne de commande : [occ](https://docs.nextcloud.com/server/9/admin_manual/configuration_server/occ_command.html)
```
su - nextcloud 
 ./occ
 Coffre-fort 16.0.4

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
      --no-warnings     Skip global warnings, show command output only
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

 ```

# A. Description du fonctionnement

## Etapes lors de la création d'un compte client / provisionning client

Les documents mis à disposition par le Coffre-Fort Electronique sont déposés sur le compte de l'administrateur principal (ayant le "nom à afficher" de la société \<nomSociete>)
L'arborescence du coffre-fort de l'administrateur "Principal" est donc organisée comme tel:

```
--
 |-CLIENT1/<nomSociete>/DossierClient
 |-CLIENT2/<nomSociete>/DossierClient
 .
 |-CLIENTn/<nomSociete>/DossierClient
```

Un nouveau compte client est un structure ne contenant que :

- un répertoire 'Personnel' 
- le répertoire partagé  par l'admin pour présenter les documents au client

Le compte client est préparé en amont par l'admin du CFE ou alors un utilisateur afin de configurer le ou les partages accessibles pour ce client.
Si le client fait parti d'une famille/indivision (un groupe ayant été crée à cette fin), le partage est effectué sur le groupe et donc automatiquement accessible à chaque membre de ce groupe.

### Pour info: repertoire 'skeleton'

Dans la configuration *config.php* , la variable 'skeletondirectory' contient le répertoire utilisé comme modèle pour un nouvel utilisateur.

Ici , le répertoire 'nextcloud-server/themes/\<nom_theme>/core/skeleton' a été choisi, et ne contient qu'un répertoire vide nommé "Personnel"

# B. Créer un espace pour un nouveau client en ligne de commande

L'avantage d'utiliser la ligne de commande est qu'aucun email de bienvenue n'est envoyé.

Sur le serveur Front :

- générer un mot de passe temporaire chiffré
```

```

- créer le compte 
```
 ./occ user:add client1 --display-name "Prenom1 CLIENT1" --group "CLIENTS<nom>" --password-from-env
 The user "client1" was created successfully
 Display name set to "Mr CLIENT1"
```

- effacer la trace du mot de passe temporaire 
```
unset OC_PASS
```
- mettre à jour les paramètres de l'utilisateur client
```
./occ user:setting client1 settings email "client1@mail.com"
./occ user:setting client1 core lang fr
./occ user:setting client1 files quota  "none"
```
- Partager du répertoine avec ce client.

# C. Créer un espace pour un groupe de client / indivision

Si 2 clients doivent se recevoir , consulter, et pouvoir se partager plusieurs documents, au sein d'une famille, ou d'une société ou d'une indivision.
- créer un groupe nommé
```
./occ group:add famille1
```

- ajouter les membres à ce groupe
```
 ./occ group:adduser famille1 client1
```
# D. Partage des documents avec la famille/indivision de personnes

- Partager le répertoire \<nom> au groupe et non plus à l'utilisateur client1 ...

# E. Configuration du comportement de partage par défaut

Dans Paramètres de l'administrateur, menu "Partage" :
sélectionner les options suivantes:

- Autoriser les applications à utiliser l'API de partage

- Autoriser les utilisateurs à partager par lien
  - Autoriser les téléversements publics
  - Toujours demander un mot de passe
  - Imposer la protection renforcée du mot de passe

- Autoriser le repartage
- Autoriser le partage avec les groupes
- Empêcher certains groupes de partager
      CLIENTS\<nom>
  
# F. (utilisation avancée)Gestion du partage en ligne de commande

Sur le serveur de  base de données 
- se connecter sur la base de données Nextcloud et lister les partages en cours
```
sudo -u postgres psql -d template1
\l
\c <nomBaseDonnees>
select * from oc_share;
```

- trouver l'id de l'élément à partager dans l'interface Web Nextcloud , dans l'URL : http://serveurFront/apps/files/?dir=/CLIENT1&fileid=\<id>
- creer un partage de l'utilisateur \<admin> vers l'utilisateur ou le groupe \<client>

```
INSERT INTO public.oc_share(
	share_type, share_with, uid_owner, uid_initiator, item_type, item_source, file_source, file_target, permissions, accepted, mail_send, password_by_talk, hide_download)
	VALUES ( 1, '<client>', ' <admin>', ' <admin>', 'folder', <id>, <id>, '/CLIENT1', 21, 0, 0, 'f', 0);
	
```
share_type = 0  pour un partage avec un utilisateur 
share_type = 1 pour  un partage avec un groupe 

- si un utilisateur de groupe supprime son partage , on peut le restaurer avec la commande:
```
DELETE FROM public.oc_share
	WHERE share_type =2 and share_with = '<utilisateur>' ;	
```