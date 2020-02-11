#--------------------------------------------------------------------------------------------------
# BUT :
# - Permet de mettre à jour un wordpress distant.
# - Exemple ci-dessous sur un serveur 1and1 mutualisé.
#
# NOTES :
# - Il faut obligatoirement des TAB pour l'indentation des lignes.
# - A executer sur votre machine local.
# - Commande (taper votre mot de passe SSH + valider ou utiliser /.ssh/authorized_keys sur votre serveur) : 
#   - make help
#   - make dev
#   - make import
#   - etc...
# - Lors de la 1er utilisation vous devez :
#   - Creer manuellement une db local (puis un : make dbimport, pour importer la db distante)
#   - Telecharger wp-config.php du serveur distant. L'adapter avec votre db local : 
#	  - DB_NAME: ma_new_db
#	  - DB_USER: root
#	  - DB_PASSWORD: root
#	  - DB_HOST: 127.0.0.1:8889
# 
# IMPORTANT : Si vous utilisez un vhost
# - Edit .htaccess local
# - Changer "RewriteBase /" par "RewriteBase /votre_dossier_dans_htdocs/"
# - Ex : "RewriteBase /mon_site/" (avec mon_site qui est le dossier sur lequel pointe votre vhost)
#--------------------------------------------------------------------------------------------------

# ENV: dev, prod
ENV=dev
ssh=u12341234@home123456789.1and1-data.host
error_custom=---- ENV:$(ENV) ----
VERSION_MAKEFILE=1.0

ifeq ($(ENV), dev)
	# Utilise pour replace en db (sans le protocole "http(s)://"):
	domain_distant=dev.nomDeMonSite.com
	domain_local=127.0.0.1:8888/nomDeMonSite

	path_distant=~/nomDeMonSite_com_dev/
	path_local=./

else ifeq ($(ENV), prod)
	# Utilise pour replace en db (sans le protocole "http(s)://"):
	domain_distant=www.nomDeMonSite.com
	domain_local=127.0.0.1:8888/nomDeMonSite

	path_distant=~/nomDeMonSite_com_prod/
	path_local=./

else
	error_custom=---- Erreur choix ENV ----
endif

#---------------------------------------------------------------
# SCRIPT CONFIG (ne rien modifier ci-dessous)
#---------------------------------------------------------------

.PHONY: help config debug dev update import dbimport deploy dbdeploy

help: ## Affiche cette aide
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

config: ## Afficher la config actuellement en place
	@echo '#####################################################'
	@echo '# CONFIG CURRENT (v$(VERSION_MAKEFILE)) :'
	@echo '#####################################################'
	@echo '# ENV :'
	@echo '#	- $(ENV)'
	@echo '# PATH :'
	@echo '#	- distant: $(path_distant)'
	@echo '#	- local: $(path_local)'
	@echo '# DOMAIN:'
	@echo '#	- distant: $(domain_distant)'
	@echo '#	- local: $(domain_local)'
	@echo '#####################################################'

debug: ## debug custom
	$(error Debug: $(error_custom))

dev: ## Lance le serveur de développement
	php -S localhost:8000 -d display_errors=1

update: ## LOCAL : update core and plugins all
	wp core update
	wp plugin update --all

test_affiche: ## Afficher : info, warning, error
	$(info test pour afficher un simple message)
	$(warning test pour afficher un warning)
	$(error test pour afficher une erreur + exit)

#---------------------------------------------------------------
# IMPORT (en local)
#---------------------------------------------------------------

import: ## Importe les fichiers distants
	rsync -av $(ssh):$(path_distant) $(path_local) \
		--exclude wp-config.php # > /dev/null 2>&1

dbimport: ## Recupere db (depuis le serveur)
	ssh $(ssh) "cd $(path_distant); wp db export --add-drop-table dump.sql"
	rsync -av $(ssh):$(path_distant)dump.sql $(path_local)
	ssh $(ssh) "rm $(path_distant)dump.sql"
	wp db import dump.sql
	wp search-replace '$(domain_distant)' '$(domain_local)'
	rm dump.sql

#---------------------------------------------------------------
# EXPORTER/DEPLOYER (vers serveur distant)
#---------------------------------------------------------------

deploy: ## Deploie new version de l'application
	rsync -av $(path_local) $(ssh):$(path_distant) \
		--exclude Makefile \
		--exclude .git \
		--exclude .idea \
		--exclude .DS_Store \
		--exclude .htaccess \
		--exclude wp-config.php \
		--exclude /wp-admin \
		--exclude /wp-includes \
		--exclude /wp-content/uploads

dbdeploy: ## Envoie les donnees sur le serveur
	wp db export --add-drop-table dump.sql
	rsync -av $(path_local)dump.sql $(ssh):$(path_distant)
	ssh $(ssh) "cd $(path_distant); wp db import dump.sql; wp search-replace '$(domain_local)' '$(domain_distant)'; rm dump.sql"
	rm dump.sql
