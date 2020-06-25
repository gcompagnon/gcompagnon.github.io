---
layout: post
title: "présentation du dév Cloud Native avec RedHat Openshit"
categories: notes openshift
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [cloud native programming]
excerpt: exemple de fonctions Excel
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

La présentation Red Hat OpenShift : l'expérience Cloud-Native pour les développeurs


![illustration](/images/20200625_IntegrationCloudNative.png)

copier/coller de l'extrait fourni par RedHat le 25/06/2020
# intro

L’approche dite “Cloud-Native” permet de développer et exécuter des applications modernes, évolutives, scalables et robustes dans tous les environnements, que ce soit un cloud public, privé ou hybride. C’est l’approche privilégiée aujourd’hui par les organisations pour concevoir, développer ou moderniser des applications dans un contexte toujours plus compétitif où délivrer rapidement la nouvelle fonctionnalité différenciante est devenue cruciale.

Avec la plateforme OpenShift, Red Hat offre l’expérience la plus productive dans l'écosystème le plus riche pour couvrir tout le cycle de vie de vos applications Cloud-Native. Nous vous proposons de le découvrir lors de cet événement avec une expérience interactive illustrant développement, déploiement et intégration avec Red Hat OpenShift et la gamme Red Hat Middleware.

Au programme de cet événement: 3 sessions de 30 minutes pour couvrir le cycle de vie complet d’une même application Cloud-Native ainsi qu’un Q&A live pour répondre à toutes vos questions !

 
# Session 1:
Boostez vos micro-services avec OpenShift Service Mesh

Laurent Broudoux et Guillaume Estrem

Cette première session couvrira l’intérêt et le déploiement de Service Mesh sur OpenShift. Ce service basé sur les projets communautaires Istio, Kiali, Jaeger et Prometheus permet découpler la couche réseau des applications afin de maîtriser la distribution, la résilience de vos microservices. 

 

# Session 2:
Développez, Testez et déployez en continu avec CodeReady Workspaces 

Laurent Broudoux

Dans cette deuxième session nous ferons évoluer notre application et discuterons de l’“inner loop” (Code - Build - Test) et de l’“outer loop” (Build - Test - Deploy - Manage). Vous découvrirez comment CodeReady Workspaces (Eclipse Che) et OpenShift Pipelines (Tekton) et OpenShift Serverless (Knative) permettent d’embarquer très rapidement, rendre autonome une équipe de développement et itérer très rapidement.

 
# Session 3:
Intégrez les données et votre existant : the cloud-native way

Nicolas Massé

La dernière session conclura sur l’intégration de flux de données distribuées avec AMQ Streams et Fuse Online basés respectivement sur Kafka et Syndesis.