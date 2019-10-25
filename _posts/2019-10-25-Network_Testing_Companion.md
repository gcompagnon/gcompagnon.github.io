---
layout: post
title: "Utilitaire Network Testing Companion"
categories: network test skype teams
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [IT network utilities teams skype ]
excerpt: Installation de l utilitaire pour tester le reseau pour Skype ou Teams
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

# Installation du module

Par Powershell
https://www.powershellgallery.com/packages/NetworkTestingCompanion/1.5.4
```
PS> Install-Module -Name NetworkTestingCompanion
```

Accepter de charger un module non signé ! :(
Changer le niveau de policy

```
PS> Get-ExecutionPolicy -List
PS> Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser
```

Installation de l icone dans le menu start
```
Invoke-ToolCreateShortcuts
```

L utilitaire est accessible par Network Testing Companion


La documentation: 
https://github.com/MicrosoftDocs/OfficeDocs-SkypeForBusiness/blob/live/Teams/downloads/network-testing-companion.zip?raw=true


# Fonctionnalités
Permet de vérifier 
- la connectivité avec l ensemble des serveurs Skype/Teams avec les protocoles HTTP HTTPS et UDB
- la bande passante
- le niveau de l'OS
- la compatibilité du casque/Micro avec Skype


# remettre la sécurité pour des modules signés
```
PS> Set-ExecutionPolicy -ExecutionPolicy Undefined -Scope CurrentUser
```
