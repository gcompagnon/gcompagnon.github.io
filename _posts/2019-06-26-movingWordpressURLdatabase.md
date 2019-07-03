---
layout: post
title: "Migrate Wordpress from URL A to URL B"
categories: wordpress migrate
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [wordpress, webhosting, ovh, migration]
---

# Wordpress // notes for migrating a WordPress site from URL A to URL B
(duplicate a webhosting OVH for getting UAT and A/B testing)

-----
Original post is on [https://gcompagnon.github.io/](https://gcompagnon.github.io/)

# Backup and recover to another WebHosting

## Backup Files

OVH is making able to add scripts into the cron

A script that makes tar file of all files (except ./bin and .backup ):
````
./bin/fullback.sh
````
=> this will create tar files in ./backup

a script that untar from a file in the rootpath of the webhosting
````
./bin/restorebackup.sh
````

scripts repository: [https://github.com/compagnon/OVH_wordpress_webhosting](https://github.com/compagnon/OVH_wordpress_webhosting)

## Backup MySQL
Dump the MySQL db


##Update User Right in MySQL (optional)

* Get user info
```
SELECT USER(),CURRENT_USER();
SHOW GRANTS ;
```

* SQL to alter the sql user with what Wordpress requires
```
GRANT USAGE ON *.* TO 'user'@'%' IDENTIFIED BY PASSWORD <secret>
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE ON `user`.* TO 'user'@'%'
```



#Changing The Site URL

https://wordpress.org/support/article/changing-the-site-url/#changing-the-url-directly-in-the-database

##In the wp-config.php , adapt the folowing lines

````
define('DB_NAME', 'databasename');

/** MySQL database username */
define('DB_USER', 'username');

/** MySQL database password */
define('DB_PASSWORD', 'password');

/** MySQL hostname */
define('DB_HOST', 'localhost');

````

##migrate the URL hardcoded in the MySQL database
=> cf mysql/wordpress_migrateURL.sql
from http://xxxx.com to https://yyyy.com
````

UPDATE wp_options SET option_value = replace(option_value, 'http://xxxx.com', 'https://yyyy.com') WHERE option_name = 'home' OR option_name = 'siteurl';
UPDATE wp_options SET option_value = replace(option_value, 'http://xxxx.com', 'https://yyyy.com') ;

UPDATE wp_posts SET guid = replace(guid, 'http://xxxx.com','https://yyyy.com');

UPDATE wp_posts SET post_content = replace(post_content, 'http://xxxx.com', 'https://yyyy.com');

UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://xxxx.com','https://yyyy.com');


````

##Change hardcoded URL inside the themes php files :

for example in 
home.php
footer.php
dans \www\wp-content\themes\<mytheme>

## <optional> Plugins  or extensions  settings  to recover
### Avada 

in Avada > Theme Options > Import / Export tab.
export a Json file from the source Wordpress wp-admin site
for import into the destination wp-admin site

### <optional>Slider Revolution
in wp-admin/ slider revolution menu: choose Slider Settings for exporting JSON

Choisirs Pages/ et en bas de page : Fusion Page Options/ Import/export

##Changing the .htaccess file
ref doc is :
https://httpd.apache.org/docs/current/fr/mod/mod_rewrite.html

WordPress REST API is mandatory to generate Pages (Fusion Builder plugin)
And Apache need to 
 / for WP Rest API not hidden , not returning HTTP 404 for /wp-json although /index.php/wp-json is HTTP OK
for rewrite rules : use the mod_rewrite for Apache



````
# for debug purpose only - see in error_log file
#LogLevel alert rewrite:trace3

# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]

RewriteCond %{SERVER_PORT} 80
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]

</IfModule>
# END WordPress
````


