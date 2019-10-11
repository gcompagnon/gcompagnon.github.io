---
layout: post
title: "Notes AWS ECS Workshop"
categories: tasks microservice container platform
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [IT architecture microservices container platform]
excerpt: Atelier technique sur l'offre AWS pour héberger une application en conteneurs (microservices/tasks/webfront/backend)
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

# Elearning AWS / ECS avec des instances  EC2 ou Fargate
par [Rémi RETUREAU](https://www.linkedin.com/in/remiretureau/)

.. Mes notes sur cet atelier AWS / Plateforme ECS ...
![plateforme ECS ](https://ecsworkshop.com/images/crystal.svg)

## Ressources
Pour faire des schéma d archi (indispensable avec une convention de nommage):

 https://cloudcraft.co/
 ou
 https://draw.io


l atelier est détaillé:

https://ecsworkshop.com/

les codes sources des 3 applications en conteneurs, et les scripts Python ou shell pour construire la platforme en ligne de commande CLI

https://github.com/brentley

[Brent Langston](https://www.linkedin.com/in/brentley/)

Les tasks sont en fait des containers: 1 front (en Rails) et 2 back ends (Node.js et Crystal)

La page officielle pour ECS:
https://docs.aws.amazon.com/fr_fr/AmazonECS/latest/developerguide/Welcome.html

Les slides (à venir . si diffusé)

## A retenir

- Il faut absolument nommer les tâches dans AWS Cloud Map 
 https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-task-definition.html

=> surtout pour suivre la consommation, les erreurs applicatives ... 


- Cloudwatch pour voir les metrics (logs) , pour scaler les taches ... (ou servir dans l autoscaling)

- Intérêt pour faire du [A/B testing](https://fr.wikipedia.org/wiki/Test_A/B)
ou  Blue-Green deploy
avec CodeDeploy
 https://aws.amazon.com/blogs/devops/use-aws-codedeploy-to-implement-blue-green-deployments-for-aws-fargate-and-amazon-ecs/

- Piloter le scaling avec des evenements/ donc sans autoscaling 
une fonction lambda permet de créer un workflow d'automatisation

# AMAZON ECS

## Task Deployment on Availability Zone

controlled by  placement constraints

Bin packing , ...

Service discovery / route 53 ... Task inside ECS -> start -> Subscribe into Route 53 (service registry)->  subscribe into Cloud Map (tasks tracking with user friendly name)

Target Tracking Autoscaling: with a CloudWatch metric , with alarms , that trigger the scaling policy => scale out/up and in (add / remove task)

## AWS Fargate ("la plateforme "Easy peasy")
with ECS (complementary)  => en réalité : il y a un choix à faire ! suivant la capacité à maitriser son devops 

- manages production workloads
- VPC Networking mode 


Run task with launch type / Fargate or EC2
Same *Task Definition schema* - easy migration

SLA : 99.99%

**For small team / orchestration easy peasy**

## ECS Network
Network : no , ou host ou bridge ou aws-vpc (fargate)
ECS could have a VPC Endpoints : AWS Private Links (no Internet tunneling)

aws-vpc: 
 Internet gateway with an public internet address, private IP, ... and networks ACL

 ## LoadBalancer 
 -Path-based routing (defines rules by URL path)
 -Dynamic Port Mapping : several container instance , tasks with the same IP, and several ports mapped
 supports:
 HTTP/2
 Websockets
 Detailed Logging

## Cluster level Isolation:
Multi tenants , but separate clusters type (prod, beta dev ...)

cluster permissions , and Application permissions (Task level)

Housekeeping permissions: ECR image pull, cloudwatch logs pushing, ENI creation ... in IAM , by default

## Containers Registry
ECR private repo for docker image, IAM credentials

=> les conteneurs sont **A SECURISER** sur la partie BUILD 

## Visibility and monitoring:
Cloud Watch Logs

Service-Levels metrics available

## Storage in EC2

ECS taks could use:
docker volumes (local , EBS or EFS)
bind mounts

Fargare task is ephemeral ...

# ECS costs and charges

Pay for ECS instances, EBS volumes , LB ...
EC2 Spot instances allow to requedst space EC2 / (cf podcast ecommerce)
https://aws.amazon.com/fr/blogs/france/podcast/#003

# DEV Stage

## CI/CD to Amazon ECS
push code -> AWS CodeCommit (ou github) -> AWS CodePipeline  2choices-> CodeBuild -> ECR  or Cloud formation -> ECS

## Cloud9
ECS instance with dev / IDE => les terminaux bash 

## GITHUB

git clone https://github.com/brentley/ecsdemo-frontend
git clone https://github.com/brentley/ecsdemo-nodejs
git clone https://github.com/brentley/ecsdemo-crystal

