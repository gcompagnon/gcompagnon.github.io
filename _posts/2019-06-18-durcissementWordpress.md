---
layout: post
title: "WordPress chez OVH (french post)"
categories: wordpress OVH
output:
  html_document:
    highlight: pygments
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [wordpress, webhosting, ovh, hebergement]    
excerpt: Notes sur le déploiement Wordpress chez OVH
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

#Configurations sur www / wordpress / php / mysql


-----

Objectif: meilleur référencement grace à la sécurisation (https par défaut) et de meilleure performance(premier affichage, optimisation par un cache)
=> améliorer son réputation web / ranking pour les systèmes anti-spam , éviter que ses emails soient rejetés par les systemes anti-spam



https://inboxpros.com/does-a-private-or-public-whois-effect-seo-email-deliverability/

# 1. OVH / configuration des domaines:

## organisation administrative 
l ensemble des domaines gérées par un seul compte dpxxxxx-ovh 

1. changements des propriétaires et correction des coordonnées pour avoir une seule entreprise propriétaire des domaines avec le dirigeant en contact adresse physique et un seul email support-web@<monDomaine>.com pour contact email

2. activer le WHOIS pour passer d'un site avec une visibilité privée à publique

3. vérifier la configuration DNS 

grace à l'outil officiel:
https://www.zonemaster.net/domain_check


conséquences sur la sécurité:
- DNSSEC activé pour éviter le cache poisoning 

conséquences sur la performance:
- DNS Anycast activé / attention car par défaut seulement DNS en France donc moins de risque d etre reconnus au niveau international

Pour avoir le score maximum, il faudrait 4 serveurs DNS , dans des zones IP différentes. OVH n efournit que 2 serveurs DNS et dans la même zone AS
( https://en.wikipedia.org/wiki/Autonomous_system_(Internet) )

## redirections sur le site principal (HTTP 301)

Pour tous les domaines autre que celui principal, aucun hébergement utilisé, prendre une configuration MultiSite pour ces domaines sur l'hébergement principal(son adresse IP v4 et IP v6)
cela correspond à une configuration A AAAA et CNAME
````
     IN A      213.186.33.x
     IN AAAA   2001:41d0:1:1b00:213:186:33:x
www  IN CNAME  <domaineSecondaire>.fr.
````

Ne pas oublier d ajouter le domaine secondaire en multisite , avec son certificat SSL pour que l'herbergement principal (mutualisé chez OVH) réponde

# 2. Améliorer la réputation des emails @domaine.com

Notre serveur email est chez Office365 (exchange server)

un outil Google permet de suivre sa réputation: (et éviter d etre sur des listes de spam)
https://postmaster.google.com/managedomains?pli=1

## Limiter la surface d attaque en supprimant les configurations d'emails inutiles
ENlever des configurations DNS , les entrées MX pour les domaines où on ne veut aucun email

## email: comme expéditeur 
vérifier la config Office365 chez OVH

https://docs.microsoft.com/fr-fr/previous-versions//jj655360%28v%3dtechnet.10%29

## redirection sur le serveur email/exchange

S'assurer de l'entrée MX et l identifiant MS
````
domaine.com.      0        MX     1 domaineGUID.mail.protection.outlook.com.
domaine.com.          0     TXT     MS=msXXXXXXX
````

en option l instruction pour la configuration automatique des applis de mail: 
````
autodiscover.domaine.com.  0 CNAME autodiscover.outlook.com.
````

option, mettre le lien webmail actif:
````
webmail.domaine.com.  0    CNAME      mail.office365.com.
````

### Mettre en place les parametres de sécurité proposés par Office 365 
### SPF (Sender Policy Framework)
https://docs.microsoft.com/en-us/office365/SecurityCompliance/how-office-365-uses-spf-to-prevent-spoofing

sur la zone DNS du domaine.com : s assurer de la présence du champ SPF :
````
domaine.com.        600     SPF     "v=spf1 include:spf.protection.outlook.com -all"
````

pour la redirection OVH https://docs.ovh.com/fr/domains/le-champ-spf/ vers Office 365

### DMARC
https://docs.microsoft.com/en-us/office365/SecurityCompliance/use-dmarc-to-validate-email
(pour etre referencer sur agari.com )
https://fr.wikipedia.org/wiki/DMARC

````
_dmarc.domaine.com.  0     DMARC    v=DMARC1; p=quarantine;
````

### DKIM Domain Keys Identified Mail
https://docs.microsoft.com/en-us/office365/SecurityCompliance/support-for-validation-of-dkim-signed-messages

https://fr.wikipedia.org/wiki/DomainKeys_Identified_Mail

````
selector1._domainkey.domaine.com.        0      CNAME   selector1-domainGUID-com01c._domainkey.domainGUID.onmicrosoft.com. 
selector2._domainkey.domaine.com.        0      CNAME   selector2-domainGUID-com01c._domainkey.domainGUID.onmicrosoft.com.
````

# 3. HEBERGEMENT SITE / WEB HOSTING
# installer un certificat SSL pour chaque domaine 
voir la doc:
https://docs.ovh.com/fr/hosting/les-certificats-ssl-sur-les-hebergements-web/

Lorsque le certificat n'est plus valide, expirée (car durée de seulement 1 an chez Lets Encrypt)
demander dans l interface OVH/Hébergement, Multsites, de regénerer le ou les certificats

## Passer en https par défaut

le fichier .htaccess doit contenir ces instructions:
````
RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
````

voir aussi:
https://blogbuster.fr/seo/migration-https-ovh.htm

## améliorer la performance du serveur Apache WWW
Impossible sur OVH en mutualisé

https://tools.pingdom.com/
https://www.dareboost.com/fr/home


# 4. Optimisation des performances de WordPress

## optimiser wordpress
https://wordpress.org/support/article/optimization/

## tester les perf 

https://www.webpagetest.org/


## ne pas oublier de rediriger les pages sur PHP

si wordpress ne l a pas fait, il faut absolument configurer Apache et .htaccess avec 
````
# BEGIN WordPress
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
# END WordPress
````
Cela permet de faire fonctionner la WP REST API

## sécuriser Wordpress en Desactivant la REST API

réduire la surface d'attaque et ne plus permettre d'acceder à /wp-json ou /index.php/wp-json depuis l adresse IP publique
Installer et activer l extension "Disable REST API for Real"
Le menu de cette extension est dans Réglages / General en bas de page 


## activer les caches sur le serveur Apache

Installer l'extension W3 Total Cache  sur wordpress

=> cela modifie le fichier .htacess du site

## Optimiser les images
Dans un premier temps, utilisation de l'extension "Smush"

# 5. reférencement / SEO

## outil pour tester le référencement (autre que google ou bing)
https://www.seo-referencement-naturel.net/fr/
https://www.ionos.fr/website-checker-result#menu-PRESENTATION


## Configurer des entrées DNS pour permettre d utiliser les outils

Pour google search console
````
domaine.com.    0   TXT   "google-site-verification=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
````

Pour bing webmaster
````
XXXXXXXXXXXXXXXXXXXXXXXXXXX.domaine.com.  0  CNAME   verify.bing.com.
````

## GOOGLE / outils pour améliorer sa visibilité /réputation
https://audreytips.com/7-outils-gratuits-google-tester-votre-site/

Google search console
https://search.google.com/search-console/performance/search-analytics?resource_id=sc-domain
## BING / outils pour améliorer sa visibilité /réputation
https://www.bing.com/webmaster/home/dashboard/

## MULTI SITE/ exploiter au mieux l ensemble des noms de domaines et des hebergements

utiliser l ensemble des noms de domaines:
avant: redirection sur le site pricinpal par une instruction TXT/CNAME
après: pointer l ensemble des domaines sur le serveur (config multisite) , cad: instruction A et AAAA sur l adresse du seveur d herbergement

Ne pas dupliquer les hébergements et donc les installations de Wordpress => cela déréférence les sites qui utilisent cette technique (voir Google Panda)

## vérifier sitemap.xml et robots.txt

S'assurer que Wordpress génére bien un sitemap.xml (ensemble des pages à référencer , ce qui améliore les recherches ensuite)
il faut un accès à https://domaine.com/sitemap.xml ou https://domaine.com/sitemap_index.xml

Il ne faut pas de fichier robots.txt ou alors sans instruction Disallow
Celui permet d'indiquer des sites à ne pas référencer (car site en test, en construction)
par exemple :
````
robots.txt:
  User-agent: *
  Disallow: /
````
permet de ne pas référencer un site complet


## Configurer les META DONNEES sur WordPress

Avec l'extension Wordpress "Yoast SEO"
il est possible d'ajouter une mini description snippet à chanque Page
=> cela générer dans l entete du source HTML (header) des instuctions meta description





