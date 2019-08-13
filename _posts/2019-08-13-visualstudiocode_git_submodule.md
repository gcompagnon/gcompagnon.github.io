---
layout: post
title: "notes on GIT inside Visual Studio Code"
categories: notes git
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [git,vs code, dev]
---

#How to get several Git repo inside the same VS Code workspace
edit a .gitmodules file inside the main repo

for example for a GIT repo (nexcloud-server fork) https://github.com/compagnon/nextcloud-server.git cloned on a folder , it can be composed of sub folder , that are GIT repo.
For example, *3rdparty* is the folder containing optional components and *custom_apps* is the folder where there are customized apps
and these paths are also GIT repo. 

Add or edit a .gitmodules to get the 3 GIT modules (and remote) available in VS Code / Source Control(Ctrl+Shift+G) as 3 distinct source control providers

For example: 

```
	[submodule "3rdparty"]
	path = 3rdparty
	url = https://github.com/nextcloud/3rdparty.git

	[submodule "nextcloud-custom_apps"]
	path = nextcloud-custom_apps
	url = https://github.com/compagnon/nextcloud-custom_apps.git
```