---
layout: post
title: "Cloudification dans les grandes lignes - Août 2019(french post)"
categories: cloud bulletpoints
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [cloud,migrate, project]
---

* *bullet points* sur la cloudification: migration de datacenter (VM/hyperviseurs) sur des techno cloud (managed *services*/ VMs / containers+k8s) 
et 
* Opinions, mes notes loin d être *paroles d'évangile* mais qui permette de conserver une trace de mon ressenti à cet instant (*milieu du gué*) sur le plus grand bouleversement que l'IT (ops et dévs) traverse depuis la révolution dotcom/ecommerce.
-- 
# Préalables - points à retenir 

## La Silver Bullet / promesse marketing du cloud
Il est simple et rapide 
de : 
=> développer une app cloud-native
=> migrer une app simpliste
=> migrer une archi de serveurs web (CDN/proxy)
=> migrer en SaaS (Attention car loin d'être Multi-cloud / cf Salesforce sur uniquement AWS)
avec 
 * un pipeline CI/CD de facto (codes sources et data de test archivés/snapshotés et avec un SLA honorable >99.90%)
 * une sécurité *by design* (si déléguée au cloudprovider)
 * l'outil d'administration / suivi de prod à coûts de run/BAU imbattables - jusqu'à la prochaine révision tarifaire du cloudprovider

Dans ces cas d'usage, les arguments mis en avant : 
* plus d'éfficacité pour les dév (donc plus de vélocité pour délivrer) , 
* une économie sur les coûts Ops (infra et personnel/FTE) , ou plus exactement 
* un passage de coûts CAPEX (immobilisation en capital-donc fixe) à OPEX (dépenses opérationnelles-donc variables)

Bien entendu, ces cas d'usage sont bien loin de recouvrir l'ensemble des cas existants dans un SI.

## Conditions à remplir pour cette promesse

Une app doit être cloud-native - cad respecter les [12-factor](https://12factor.net/fr/) 
Pour les projets de dév: des frameworks cloud-native basés sur les archi serverless:  microservices/stateless services (noms commercials: AWS Lambda - Azure et GCP functions) sont proposés.

de plus, sans un pipeline CI/CD (ou un outil de déploiement) performant et un ou des outils de supervision globaux de type [Security information management system](https://fr.wikipedia.org/wiki/Security_information_management_system) , le cloud est juste impensable.

# Concessions

## Colocation pour raison économique

L'aspect économique est le facteur mis en avant, et en contrepartie, il faut accepter:
    ne plus gérer l'emplacement physique des instances dans le chassis / dans le datacenter => le cloud n'est pas optimale avec du baremetal et des conf très specifiques.
    la colocation de ces apps avec d'autre apps de la même orga, (cas cloud privé)
    la colocation des ces apps avec d'autres orga.

# Plusieurs étapes dans la cloudification / stratégie Shift-Translate

## on premise

Etape initiale

## cloud privé interne/onsite (IaaS -> PaaS)

Première étape possible dans le cas: 
* des servers  dans son propre datacenter sont disponibles pour être recyclés 
* des compétences sont dispo en interne pour gérer un IaaS ou même un PaaS

Afin de limiter le risque d'execution (*marche atteignable*) 

## cloud privé externe

pour libérer son propre DC, se convaincre que ses app sont conformes à des standards.

## Cloud public

Etape ultime pour des raisons économiques, de rapidité à provisionner, de mobilité
Aucun isolation matérielle (cf Intel spectre) et logicielle, implique une attention ultime sur la sécurité / chiffrement des données end to end

## Cloud privé parcours pour limiter le risque d'execution: 
Le cloud public est le moins cher/le moins *sûr* (partage de ressources CPU/pas d isolation  ),
le cloud privé (corporate cloud) semble bien la première étape atteignable / un premier sas en interne pour préparer le passage sur les infra du cloud provider

Il faut s'assurer que le cloud privé n'est pas la fin en soi car la promesse d'économie ne serait sans doute pas atteinte.
 

# Cloudification en mode projet

Evidemment, migrer un progiciel *critique* , complexe (front to back , messagerie) 
            migrer. archi 3 tiers de type Java EE, ou serveur avec des besoins RAM/CPU spécifiques (pour les prix - vol - VaR - Montecarlo ... des produits financiers))
soulève des difficultés *d'execution* et technique ... ce qui nécessite l'évaluation chiffrée (faisabilité de monter une équipe projet en parallèle du BAU, risques à faire / ne pas faire)

Une cloudification est un programme complexe car , cela nécessite à la fois:
    une orga transverse : avec un budget démarrage d'un cloud privée onsite 
    n'est pas un programme transverse , et nécessite clairement de solliciter les stakeholders des app. rester à un niveau transverse ne servira à rien.

    une politique de conduite aux changements : 
        * culturel avec une perte d'autonomie, explosion des silos 
        * organisationnel / RACI à revoir
        * technique avec des migrations d'archi



## Choix des cibles: faire appel à l' urbanisation / archi d'entreprise 
Utiliser la cartographie fonctionnelle et applicative (niveau bas de la granularité: départements / sous départements / business lines)
Utiliser pour chaque *fonction* et *appli* , les  à 6mois - 2 ans - 5 ans (évidemment ne pas investir sur une activité/appli obsolescente en décommissionnement)

Dans l ecosysteme appli, scorer pour obtenir ce qui est atteignable  en 6 Mois / 2ans / 5 ans (cad pour défendre une enveloppe pluri-annuelle budget)


## Identifier les stakeholders

Pour chaque appli candidat / suivant son RACI:
- Sponsor ou alors product owner - obligatoirement
- showstopper
- resp. technique/ technical head

Avoir un SPOC sur chaque entité transversale (ops, sécu, archi IT, EA, achat, ...) 



## Spec / SLA
 [SLA](https://fr.wikipedia.org/wiki/Service-level_agreement) averé sur les instances d'une même zone (>99.95%) mais pas garanti (ou alors chèrement avec Multi-Region cad prévoir des traitements de réplication avec latence) - impossible de contractualiser une perte d'activité (ce qui ) 

## GO / NO-GO
Il est raisonnable de bien avoir le signoff des stakeholders pour lancer une premiere phase projet

## PostMortem en fin de phase

### Gains de la cloudification - Points de réussite

On peut évaluer la réussite d'une app dans le cloud par :

plus rapide( self service) moins couteux de déployer des environnements de tests (TU / UAT voire intégration, dans la version voulue) => encourager la qualité en se focalisation sur le test avec moins d'efforts parasitant 

A suivre ...