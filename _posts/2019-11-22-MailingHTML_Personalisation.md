---
layout: post
title: "Comment envoyer des emails de type Mailchimp (en HTML)"
categories: notes email
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [mailchimp, html]
excerpt: Mode d'emploi pour envoyer des mailings "à la mailchimp" en HTML (french post)
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

Ce document décrit comment envoyer des emails personalisés "HTML", conçus en utilisant les mêmes modèles que des diffuseurs professionnels comme Mailchimp.

![alt text](https://eep.io/images/yzco4xsimv0y/3ROUB0dVHGC28OWgAaUyCC/1e2aeccf29f0bc82e1a2c23485ae6b51/MC-Horizontal.png)


# Préambule:

Les modèles / template utilisés sont basés sur ceux que mailchimp  propose à ses clients pour sa plateforme.

[https://github.com/mailchimp/email-blueprints](https://github.com/mailchimp/email-blueprints)

Les fichiers utilisés sont: 
- templates/simple-letterhead-leftlogo.html pour l'entête
- templates/simple-postcard.html 
et
- templates/fancy-variablelayout.html

Ces modèles ont permis de créer des emails personnalisés :

- agg.NouveauMr.html
- agg.NouveauMme.html
- agr.NouveauMr.html
- agr.NouveauMme.html
- mgg.NouveauMr.html
- mgg.NouveauMme.html
- mdl.NouveauMr.html
- mdl.NouveauMme.html
etc
Ces modèles sont paramètrables avec des variables comme email@here.com par exemple, qui est à modifier avec l'adresse du destinataire.

# Synthèse:

Un email personnalisé peut être choisi afin de servir de base à une communication individuelle (disons à nom@xxxx.com)
 Un script (WSL sur Windows10) permet de remplacer la variable email@here.com par un email (nom@xxxx.com)  , et de sauver le fichier dans un lot "mailing1"

~~~~
sed 's/email@here.com/nom@xxxxx.com /g' template/agr.NouveauMme.html   > mailing1/nom_xxx.html
~~~~


# Mozilla Thunderbird

 Thunderbird est un outil des gestion d'emails qui permet l'envoi d'email au format HTML.
## Installation

Veuillez télécharger le programme :
[https://www.thunderbird.net/fr/](https://www.thunderbird.net/fr/)

et suivre la procédure d'installation:


- Passer la première étape et utiliser mon adresse existante

![alt text](https://user-media-prod-cdn.itsre-sumo.mozilla.net/uploads/gallery/images/2017-08-19-08-43-11-9039f7.png)



![alt text](https://user-media-prod-cdn.itsre-sumo.mozilla.net/uploads/gallery/images/2014-03-12-06-41-09-136476.png)

- remplir la case Vos nom et prénom avec le libellé (au choix) qui figura comme expéditeur dans les emails

- remplir la case adresse électronique et mot de passe avec les identifiants de la messagerie

A la fin de cette étape, Thunderbird a configuré une nouvelle boite aux lettres.

## Rédiger un email en utilisant HTML

Pour composer un nouveau message : 

- À partir du bouton du menu de l’application : ![alt text](https://user-media-prod-cdn.itsre-sumo.mozilla.net/uploads/gallery/images/2014-01-10-13-08-08-f52b8c.png)
Cliquez sur le menu de l’application  . 
Sélectionnez Nouveau message et Message dans le sous-menu. 

OU 

- Cliquez sur Écrire dans la barre d’outils de Thunderbird. 

OU 
- Dans la barre de menu de Thunderbird, cliquez sur Messages et choisissez Nouveau message


Pour inclure le contenu d'un fichier HTML 

- Cliquez sur la zone du message .
Cliquez sur Insérer dans la barre d’outils de Thunderbird. 
Choisir HTML dans le menu 
Copier le contenu du fichier HTML (ouvert au préalable dans Notepad) et coller dans la zone de champ Libre
Cliquer sur Insérer

## Automatiser l envoi en ligne de commande
voir 
[http://kb.mozillazine.org/Command_line_arguments_%28Thunderbird%29](http://kb.mozillazine.org/Command_line_arguments_%28Thunderbird%29)

A compléter...