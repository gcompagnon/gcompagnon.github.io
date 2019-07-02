---
layout: post
title: "nextcloud and their container process"
categories: container docker pipeline software factory
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [container, nextcloud]
---
Nextcloud.org (or Owncloud.org) is very nice *peace* of *software* ( PHP CSS JavaScript ) implementing a "files cloud" as Dropbox / Google Drive / Microsoft OneDrive / Apple iCloud / ...

Building platform is a Windows 10 with WSL / Debian fetch.


The objective is to customize the default nextcloud , more than with CSS and logo images. Idea is to be able to override or add some internal file (patch) during the Docker / container process. Hacking/Overriding Dockerfile is what it is attented to be. Nextcloud is a sort of microservice , that could be scaled thanks to containers and an orchestrator ( aka K8s Kubernetes)

BUT : no doc for "how to build the docker images of nextcloud"
from github https://github.com/nextcloud/docker ( readme.md is aimed to how to use docker images of nextcloud , found in hub.docker.com )

There is not one single Nextcloud image but several images for <li>each versions (... 13 14 15 16 always the latest)</li>  <li>each servers engines : apache fpm fpm-alpine</li> and  <li>each IT architecture : amd64 (intel/amd 64bits) i386 (32bits)</li>

[fpm](https://php-fpm.org/) is a fastCGI PHP engine , meaning an optimized PHP Engine, in comparison of apache's PHP mod. Nevertheless fpm is not a HTTP server, meaning you need an additional server supporting FastCGI protcol to serve files (html, css, img, png ...).
fpm-alpine is built from an image based on [Alpine Linux](https://www.alpinelinux.org/) and the FPM engine. the smaller image possible but with some [security breaches/hardcoded credentials](https://securityaffairs.co/wordpress/85251/breaking-news/hardcoded-credentials-alpine-linux-docker.html).
apache is built from php reference implementation / <mark>mod_php</mark>  of [Apache](https://docs.docker.com/samples/library/php/)
The most secure is to compose **fpm**  with **[nginx](https://docs.docker.com/samples/library/nginx/)** or [haproxy](https://docs.docker.com/samples/library/haproxy/) or [caddy](https://caddyserver.com/) images


Nextcloud has setup a quite complex process to generate all docker files by 2 commands : **update and generate-stackbrew-library**: One for dumb duplicate of file materials , and the other to cook / brew with the latest modules.


```{r, engine='bash', eval = FALSE}
gui@SAGIS-09:/mnt/c/localGIT/compagnon/nextcloud-docker$ ./update.sh
updating 16.0.1 [16.0] apache
updating 16.0.1 [16.0] fpm
updating 16.0.1 [16.0] fpm-alpine
updating 15.0.8 [15.0] apache
updating 15.0.8 [15.0] fpm
updating 15.0.8 [15.0] fpm-alpine
updating 14.0.12 [14.0] apache
updating 14.0.12 [14.0] fpm
updating 14.0.12 [14.0] fpm-alpine
updating 13.0.12 [13.0] apache
updating 13.0.12 [13.0] fpm
updating 13.0.12 [13.0] fpm-alpine
```
generate the directories skeleton, and the necessary files : Dockerfile for each combination / cocktail / mixture /blend  
and the travis-ci.com files also ( in Github, Travis CI could be triggered to build all the docker images)

using the *template* Dockerfile-.template, docker-*.

```{r, engine='bash', eval = FALSE}
gui@SAGIS-09:/mnt/c/localGIT/compagnon/nextcloud-docker$ export PATH=$PATH:/mnt/c/localGIT/docker/docker-official-images/bashbrew/bin

gui@SAGIS-09:/mnt/c/localGIT/compagnon/nextcloud-docker$ ./generate-stackbrew-library.sh
# This file is generated via https://github.com/nextcloud/docker/blob/d2ccd7df0493a4555fdac17ccc3d2665a4075f30/generate-stackbrew-library.sh

Maintainers: Nextcloud <docker@nextcloud.com> (@nextcloud)
GitRepo: https://github.com/nextcloud/docker.git
latest version :16.0.1

Tags: 13.0.12-apache, 13.0-apache, 13-apache, 13.0.12, 13.0, 13
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: cd04baa73d458fd19beb4d1916159baf9b819f8b
Directory: 13.0/apache

Tags: 13.0.12-fpm, 13.0-fpm, 13-fpm
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: cd04baa73d458fd19beb4d1916159baf9b819f8b
Directory: 13.0/fpm

Tags: 13.0.12-fpm-alpine, 13.0-fpm-alpine, 13-fpm-alpine
Architectures: amd64, arm32v6, arm32v7, arm64v8, i386, ppc64le
GitCommit: cd04baa73d458fd19beb4d1916159baf9b819f8b
Directory: 13.0/fpm-alpine

Tags: 14.0.12-apache, 14.0-apache, 14-apache, 14.0.12, 14.0, 14
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 14.0/apache

Tags: 14.0.12-fpm, 14.0-fpm, 14-fpm
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 14.0/fpm

Tags: 14.0.12-fpm-alpine, 14.0-fpm-alpine, 14-fpm-alpine
Architectures: amd64, arm32v6, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 14.0/fpm-alpine

Tags: 15.0.8-apache, 15.0-apache, 15-apache, production-apache, 15.0.8, 15.0, 15, production
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 15.0/apache

Tags: 15.0.8-fpm, 15.0-fpm, 15-fpm, production-fpm
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 15.0/fpm

Tags: 15.0.8-fpm-alpine, 15.0-fpm-alpine, 15-fpm-alpine, production-fpm-alpine
Architectures: amd64, arm32v6, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 15.0/fpm-alpine

Tags: 16.0.1-apache, 16.0-apache, 16-apache, apache, stable-apache, 16.0.1, 16.0, 16, latest, stable
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 16.0/apache

Tags: 16.0.1-fpm, 16.0-fpm, 16-fpm, fpm, stable-fpm
Architectures: amd64, arm32v5, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 16.0/fpm

Tags: 16.0.1-fpm-alpine, 16.0-fpm-alpine, 16-fpm-alpine, fpm-alpine, stable-fpm-alpine
Architectures: amd64, arm32v6, arm32v7, arm64v8, i386, ppc64le
GitCommit: 6357f08f2559cbb7880b82e24bd07305c828a553
Directory: 16.0/fpm-alpine
``` 
this script is using ```bashbrew ``` from https://github.com/docker-library 
this util makes possible to read from a given image inside the Docker Hub (https://github.com/docker-library/official-images/raw/master/library/) :
   <li>Tags: major version number and the latest/stable sub-version</li>
   <li>The Git commit ID : to check it is the right patches, registered by the official images provider (here : nextcloud org and their repo)</li>
   <li>A fixed directory : for the image , given that the version and its core engine (variant) are the immutable key of the directory : for ex:  16.0/fpm 16.0/fpm-alpine 16.0/apache<li>

for being able to update with patches/variant/subversions all the Dockerfile for major version ( whose directories are found inside repository)

The process is able to brew / cook all the recipes with the latest subversion

-----------
At this step: Dockerfile are available for each latest release of major version( 13.0 14.0 15.0 16.0 ...) and for each base layer / php engine (apache or fpm or fpm-alpine)

Finally , if Travis-CI.com is not used under <mark>CI/CD Pipelines</mark>:

### 1-Making PGP working on [WSL Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10)


install gnupg and dirmngr (if not available on your Debian / Ubuntu )
```{r, engine='bash', eval = FALSE}
gui@SAGIS-09:/mnt/c/localGIT/compagnon/nextcloud-docker$ sudo apt-get install -y --no-install-recommends gnupg 

gui@SAGIS-09:/mnt/c/localGIT/compagnon/nextcloud-docker$ sudo apt-get install -y --no-install-recommends dirmngr 
```    

Issue importing gpg key: gpg: keyserver receive failed: Cannot assign requested address
This requires to open the firewall for PGP get certificates
![alt text](/images/20190520-nextcloudDocker-WindowsDefender.png "Windows Defender to authorize PGP server")
and 
disable IPV6 if not available on your private LAN
```{r, engine='bash', eval = FALSE}
gui@SAGIS-09:/mnt/c/localGIT/compagnon/nextcloud-docker$ mkdir ~/.gnupg && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
```
in Docker build , this is :
```
mkdir $(GNUPGHOME)/.gnupg && echo "disable-ipv6" >> $(GNUPGHOME)/.gnupg/dirmngr.conf \
```

Issue 2: gpg: keyserver receive failed: Resource temporarily unavailable
It is coming from an issue [dirmngr on WSL](https://github.com/MicrosoftDocs/azure-docs-cli/issues/1164 ) . 

<mark>keyserver URI must be changed from ha.pool.sks-keyservers.net to hkp://ha.pool.sks-keyservers.net:80</mark>
```
in debug mode
gpg: DBG: chan_3 -> KEYSERVER --clear hkp://ha.pool.sks-keyservers.net
gpg: DBG: chan_3 <- OK
gpg: DBG: chan_3 -> KS_GET -- 0x28806A878AE423A28372792ED75899B9A724937A
gpg: DBG: chan_3 <- ERR 167804934 Resource temporarily unavailable <Dirmngr>
gpg: keyserver receive failed: Resource temporarily unavailable
```
test with 
```{r, engine='bash', eval = FALSE}
gui@SAGIS-09:/mnt/c/localGIT/compagnon/nextcloud-docker$ gpg  --batch --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-keys 28806A878AE423A28372792ED75899B9A724937A;

gpg: key D75899B9A724937A: 10 signatures not checked due to missing keys
gpg: key D75899B9A724937A: "Nextcloud Security <security@nextcloud.com>" not changed
gpg: 0 keys processed (0 validity counts cleared)
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:               imported: 1
```



```{r, engine='bash', eval = FALSE}
docker build -t gcompagnon/nextcloud-fpm:15.0.8 15.0/fpm
```      

In the docker-official-images clone directory (git clone https://github.com/docker-library/official-images.git)

```{r, engine='bash', eval = FALSE}
/official-images/test/run.sh "nextcloud-15"
```      

```{r, engine='bash', eval = FALSE}
      .travis/test-example-dockerfiles.sh "$image"
```      
      
