---
layout: post
title: "Notes AWS EKS Workshop"
categories: container platform k8s
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [IT architecture k8s container platform]
excerpt: Atelier technique sur l'offre AWS pour exécuter une plateforme Kubernetes / EKS
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

# Elearning AWS / EKS
par - [Rémi RETUREAU](https://www.linkedin.com/in/remiretureau/)
    - Roberto Migli et
    - Patrick Madec


.. Mes notes sur cet atelier AWS / Plateforme ECS ...
[18 octobre 2019](https://www.aws.training/learningobject/ilt?id=37901)

Autre workshop avec un focus [ECS/conteneurs](https://www.aws.training/learningobject/ilt?id=31682)

![plateforme EKS ](/images/20191018-AWSworkshop/architecture_control_and_data_overview-.png)

## Ressources
Créer un cluster EKS en 15 min avec les modèles (stack) Cloudformation et Weaveworks EKSCTL 
install de eksctl :
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```
creation du cluster
```
eksctl create cluster --name=eksworkshop-eksctl --nodes=3 --alb-ingress-access --region=${AWS_REGION}
```
Heptio est utilisé par EKSCTL pour faire le lien entre IAM d'AWS et le coeur K8s

A savoir que JQ est un utilitaire shell permettant de processer du JSON (JSON Processor) => utile avec les config k8s

Créer un pod avec une liaison sur du Storage EBS (Block Store) ou EFS (Elastic File System) :
Créer une classe de stockage: https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html pour l'utiliser dans les pods


créer une ressource dans le cluster , avec du code YAML , JSON,  pour une appli dans un pod
![create app ](/images/20191018-AWSworkshop/dashboardK8s_CreateApp.png)

http://kubernetes.io/docs/user-guide/deploying-applications/


Pour aller plus loin que RBAC (habilitation au niveau de l'API, du CLI), pour limiter les actions applicatives comme par exemple :
```
kubectl scale deployment ecsdemo-nodejs --replicas=0 ou 1000000
```
Il faut ajouter un PolicyEngine  https://www.openpolicyagent.org/

l atelier est détaillé:
[site eksworkshop.com](https://eksworkshop.com/)

les codes sources des 3 applications en conteneurs microservices , et les scripts Python ou shell pour construire la platforme en ligne de commande CLI

[https://github.com/brentley](https://github.com/brentley)

[Brent Langston](https://www.linkedin.com/in/brentley/)


La page officielle pour EKS:
https://docs.aws.amazon.com/eks/latest/developerguide/Welcome.html

Les slides (à venir . si diffusé)

## A retenir

- Il faut absolument créer des namespaces dans K8s afin de gérer les habilitations
https://eksworkshop.com/intro_to_rbac/

=> surtout pour etre multitenant sur un gros k8s


- Le dashboard K8s https://eksworkshop.com/dashboard/ est indispensable car la console AWS ne permettra pas de voir ce qui ce passe dans les pods (instance EC2)


# AMAZON EKS

les fichiers dans CLoud9 / IDE en SaaS 
![environnement cloud9 ](https://gcompagnon.github.io/files/Guillaume_EKS.zip)

Le workshop consiste à configurer des habilitations RBAC / Role Base Authorization Control
à déployer des microservices, installer HELM et quelques Chart (le caralogue de composants/ market store)
## construction par EKSCTL 
de Weaveworks

![EKSCTL CF ](/images/20191018-AWSworkshop/CloudFormation_EKSCTL1.png)
![EKSCTL CF ](/images/20191018-AWSworkshop/CloudFormation_EKSCTL2.png)
![EKSCTL CF ](/images/20191018-AWSworkshop/CloudFormation_EKSCTL3.png)

## le tableau de bord de Kubernetes

Dans la console AWS, un cluster EKS est une liste de VMs EC2 ...
![EKSCTL CF ](/images/20191018-AWSworkshop/InstancesEC2_EKSCTL.png)

Le dahsboard est plus détaillé:

![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s.png)

Les daemons set 
![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s_daemonSets.png)

Les conteneurs déployés 
![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s_deployment_allnamespaces.png)

La liste des pods (1 pod = 1+ conteneurs)
![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s_pods_allnamespaces.png)

en phase de montée en charge (scale out) avec 
```
kubectl scale deployment ecsdemo-frontend --replicas=3 
```
quelques secondes sont nécessaires
![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s_nodes_before_scaleOut.png)
![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s_pending.png)

et baisse de charge (scale in)
![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s_nodes_after_scaleOut.png)

les services (API) déployées avec les URLs endpoints accessibles via un loadbalancer (ou un proxy)
![EKSCTL CF ](/images/20191018-AWSworkshop/dashboardK8s_services.png)
