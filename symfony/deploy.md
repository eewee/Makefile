# Deploy symfony sur serveur 1and1
## SUR SERVEUR DISTANT :
* cd /kunden/homepages/12/d123456789/htdocs/var/git
* mkdir sf4.git
* cd sf4.git
* git init --bare
* cd  hooks
* vi post-receive

__Edit post-receive ET coller ceci :__

#!/bin/sh

git --work-tree=/kunden/homepages/12/d123456789/htdocs/sf4/ --git-dir=/kunden/homepages/12/d123456789/htdocs/var/git/sf4.git checkout -f

__Après avoir save le fichier post-receive, faire :__
* chmod +x post-receive

## SUR SERVEUR LOCAL (dans le dossier du projet symfony en local) :
* git init
* git remote add live ssh://u12341234@home123456789.1and1-data.host/kunden/homepages/12/d123456789/htdocs/var/git/sf4.git

* git add .
* git commit -m "First deploy with git"
* git push live master

## SUR SERVEUR DISTANT :

* php composer.phar self-update
* php composer.phar update
* php composer.phar install --no-dev --optimize-autoloader
* php bin/console cache:clear --env=prod --no-debug
* php bin/console assets:install

## SSH 1and1 :
### Générer key :
* cd .ssh
* ssh-keygen -t rsa -C “your_email@tld.com” -b 4096
* /Users/xxx/.ssh/1and1_rsa
* mdp (optionnel)

### Copier contenu 1and1_rsa.pub (local) dans ~/.ssh/authorized_keys (distant à la racine du serveur 1and1).
on peut le faire en uploadant 1and1_rsa.pub sur le serveur.
* cat id_rsa.pub >> ~/.ssh/authorized_keys

### En local :
* cd ~/.ssh
* ssh-add 1and1_rsa

### test en indiquant : 
* ssh u12341234@home123456789.1and1-data.host ls
(On doit avoir le rendu sans devoir indiquer le mot de passe ssh)

## DIVERS :
### install composer
curl -sS https://getcomposer.org/installer | /usr/bin/php7.1-cli

### update composer
/usr/bin/php7.1-cli composer.phar self-update

### clone :
git clone ssh://u12341234@home123456789.1and1-data.host/kunden/homepages/12/d123456789/htdocs/var/git/sf4.git

### pull :
git pull ssh://u12341234@home123456789.1and1-data.host/kunden/homepages/12/d123456789/htdocs/var/git/sf4.git master
