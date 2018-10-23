#--------------------------------------------------------------------------------------------------
# BUT :
# Permet de mettre à jour un wordpress distant.
# Exemple ci-dessous sur un serveur 1and1 mutualisé.
#
# NOTE :
# - Il faut obligatoirement des TAB pour l'indentation des lignes.
# - Faire : make help, ou make import (taper votre mot de passe SSH + valider), ou ...
#--------------------------------------------------------------------------------------------------

.PHONY: help dev import deploy dbimport dbdeploy update

# Dossier distant :
path=~/wp/

# SSH :
ssh=u12345678@home123456789.1and1-data.host

# Domaine pour replace en db :
domaindist=your_website.tld
domainlocal=wp.oo:8888

help: ## Affiche cette aide
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

dev: ## Lance le serveur de développement
	php -S localhost:8000 -d display_errors=1

import: ## Importe les fichiers distants
	rsync -av $(ssh):$(path) ./ \
		--exclude wp-config.php

deploy: ## Déploie une nouvelle version de l'application
	rsync -av ./ $(ssh):$(path) \
		--exclude Makefile \
		--exclude .git \
		--exclude .idea \
		--exclude wp-config.php \
		--exclude /wp-admin \
		--exclude /wp-includes \
		--exclude /wp-content/uploads

dbdeploy: ## Envoie les donnees sur le serveur
	php wp-cli.phar db export --add-drop-table dump.sql
	rsync -av ./dump.sql $(ssh):$(path)
	ssh $(ssh) "cd $(path); /usr/bin/php7.1-cli wp-cli.phar db import dump.sql; /usr/bin/php7.1-cli wp-cli.phar search-replace '$(domainlocal)' '$(domaindist)'; rm dump.sql"
	rm dump.sql

dbimport: ## Récupère les données depuis le serveur
	ssh $(ssh) "cd $(path); /usr/bin/php7.1-cli wp-cli.phar db export --add-drop-table dump.sql"
	rsync -av $(ssh):$(path)dump.sql ./
	ssh $(ssh) "rm $(path)dump.sql"
	php wp-cli.phar db import dump.sql
	php wp-cli.phar search-replace '$(domaindist)' '$(domainlocal)'
	rm dump.sql

update: ## LOCAL : update core and plugins all
	php wp-cli.phar core update
	php wp-cli.phar plugin update --all

