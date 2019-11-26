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
excerpt: Setup VS Code with several GIT repositories, modules
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

How to get several Git repo inside the same VS Code workspace
=============================================================
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
	path = custom_apps
	url = https://github.com/compagnon/nextcloud-custom_apps.git
```

get the code:
```
 git submodule update --init
 ```


How to crewind 1 commit
======================================================
```
git checkout <feature_branch>
git reset HEAD~1
git reset --hard HEAD~1
```

```
git push --force-with-lease origin <feature_branch>
```



```
git commit amend --author "Author Name <email@address.com>" --signoff
```

How to cherry pick commits / squash commits on a branch before the rebase
======================================================

Objective is to squash lot of commits into just one

Rewind before the pile of commits
```
git checkout <feature_branch>
git reset --hard <sha1-commit-id>
git push origin HEAD --force
```

Choose some commit / cherry pick

```
git cherry-pick C
 ```

Rebase and merge 
```
 git rebase master
 ```


 