---
layout: post
title: "Robustification des conteneurs / proposition Talk meetup SecOps(french post)"
#title: "Stronger containers / DevSecOps"
categories: container docker security
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [container, docker, security, secops]    
excerpt: Présentation des faiblesses des conteneurs en terme de sécurité et des solutions
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

Robustification des conteneurs | 
Les conteneurs sont dans l'oeil du cyclone médiatique: à la fois un rêve de dév agiliste et un cauchemar pour le devSecOps, avec son lot de vulnérabilités (CVE-2019-5736). Comme le conteneur (docker) est le système d'abstraction mis en avant par les plateformes cloud, dans leurs offres XaaS, cloud public et aussi IoT, ce talk a comme objectif de faire un tour rapide des principaux risques encourus, des bonnes pratiques et des nombreuses solutions en 2019 :
en prévention : provisioning dans un registry, scan, contrôle d'intégrité d'image, la construction d'image dans le SDLC/pipeline,
 et en protection d'un run (managed kubernetes)

---

<object data="/pdf/20190617_SecOps_meetup.pdf" width="850" height="650" type='application/pdf'/>

-------------
#Les 7 propriétés d'un système robuste

#Les principaux risques

#Les avantages de la conteneurisation / le manque de priorité de Docker sur la sécurité

https://www.guillaume-leduc.fr/docker-comme-solution-de-virtualisation-theorie.html

#Virtualisation contre Containérisation / Micro-segementation

https://zwischenzugs.com/2017/10/23/a-checklist-for-docker-in-the-enterprise-updated/

#orchestrateur /Kubernetes  et risques démultipliés
31,000 contributeurs ... imaginez le code-review

#Solutions 
##Bonnes pratiques / distroless / pas de mot de passe hardcodé - inspect  / isolement / pas de root - gestion des utilisateurs applicatifs / répo certifié / limiter la surface d'attaque

vérifier les signatures ne suffit pas !
```
gpg: Signature made Wed 15 May 2019 03:05:06 PM CEST
gpg:                using RSA key D75899B9A724937A
gpg: Good signature from "Nextcloud Security <security@nextcloud.com>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 2880 6A87 8AE4 23A2 8372  792E D758 99B9 A724 937A
```

ne pas envoyer les infos des server HTTP
par exemple pour nginx!
server_tokens off;


Mise à jour car failles comme [CVE 2019-5736](https://nvd.nist.gov/vuln/detail/CVE-2019-5736) dans runC https://net-security.fr/security/cve-2019-5736-dans-runc/

Dockerfile best practices
https://www.docker.com/dockercon/2019-videos?watch=dockerfile-best-practices

https://www.tripwire.com/state-of-security/devops/container-security-docker-kubernetes/

https://kubernetes-security.info/
https://www.cncf.io/blog/2019/01/14/9-kubernetes-security-best-practices-everyone-must-follow/
https://rancher.com/blog/2019/2019-01-17-101-more-kubernetes-security-best-practices/

https://edenmal.moe/post/2018/KubeCon-Copenhagen-2018-Day-2/

file:///C:/Users/Guillaumec/OneDrive/Public/container_orchestrator/KubernetesSecurity.pdf
file:///C:/Users/Guillaumec/OneDrive/Public/container_orchestrator/GeekGuide-Twistlock-DeployingKubernetes.pdf


https://cloudnaive.online/
https://github.com/mhausenblas/cloudnaive.online
https://github.com/mhausenblas/boring-is-cool
http://containerz.info/

So containers are really just Linux process groups on steroids. Using Linux kernel features such as namespaces, cgroups, and copy-on-write filesystems, containers allow you to manage application-level dependencies, such as runtimes or libraries:
![alt text](/images/20190522-containers.png "LXC / ")
In contrast to virtual machines (VM), all containers on a machine share the same Linux kernel. Under the Open Container Initiative (OCI), both the container runtime aspects as well as the container image format has been de-fact standardized.
For cases where the learning curve and operational overhead of a full-blown container orchestrator such as Kubernetes can not be justified, you can run single containers supervised by systemd or, if you run in the public cloud, leverage offerings such as AWS Fargate or Azure Container Instances.


https://cloud.google.com/container-registry/docs/get-image-vulnerabilities

## Docker Security (docker enterprise) 

##gVisor
##Dive (util pour lister les layers)
##Sysdig
https://www.sysdig.com/

##OPA https://www.openpolicyagent.org/ K8s Admission Control / HTTP API Authorization

les sidecar :
##AquaSec , NVidia (red hat)cri-o cri-containerd , Sysdig Secure 
##stackRox
##Twistlock
##Ingress service (ISTIO(envoy) / GLOO by Solo.io)
##KATA containers
##Neuvector
##sonatype
##Tigera Zeor Trust Network Security
##Log Management/ Monitoring (elastic SumoLogic guardDuty Datadog prometheus)
##OSSEC-docker

##Hypervisor based container frameworks
Hyper Container https://hypercontainer.io/
Clear Container https://github.com/clearcontainers/runtime/wiki
frakti  https://github.com/kubernetes/frakti
Kata https://github.com/kubernetes/frakti

https://asksendai.com/devops-sre/container/overview/


https://www.lemagit.fr/tribune/Introduction-aux-risques-et-a-la-securisation-de-Docker-1e-partie

https://www.lemagit.fr/tribune/Introduction-aux-risques-et-a-la-securisation-de-Docker-2e-partie