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
#   - etc ...
#   Ou avec variable :
#   - make import mavariable=value
#   - etc ...
# - Lors de la 1er utilisation vous devez :
#   - Creer manuellement une db local (puis un : make dbimport, pour importer la db distante)
#   - Telecharger wp-config.php du serveur distant. L'adapter avec votre db local : 
#	  - DB_NAME: ma_new_db
#	  - DB_USER: root
#	  - DB_PASSWORD: root
#	  - DB_HOST: 127.0.0.1:3306
# 
# IMPORTANT : Si vous utilisez un vhost
# - Edit .htaccess local
# - Changer "RewriteBase /" par "RewriteBase /votre_dossier_dans_htdocs/"
# - Ex : "RewriteBase /mon_site/" (avec mon_site qui est le dossier sur lequel pointe votre vhost)
# 
# DOCUMENTATION :
# - https://makefiletutorial.com
#--------------------------------------------------------------------------------------------------

# ENV: dev, prod
ENV=dev
ssh=u12345678@home123456789.1and1-data.host
error_custom=---- ENV:$(ENV) ----
VERSION_MAKEFILE=1.0

# Seulement utilise pour une nouvelle installation de WP (en local)
admin_email_local=wp_test_makefile@tld.com
admin_user_local=admin
admin_password_local=admin
dbhost_local=127.0.0.1:3306
dbname_local=wp_test_$(shell date +%Y-%m-%d_%H-%m-%S)
dbuser_local=root
dbpass_local=root
dbprefix_local=wTESTp_

ifeq ($(ENV), dev)
	# Utilise pour replace en db (sans le protocole "http(s)://"):
	domain_distant=dev.monSite.com
	domain_local=127.0.0.1:80/test_makefile

	path_distant=~/monsite_com_dev/
	path_local=./

else ifeq ($(ENV), prod)
	# Utilise pour replace en db (sans le protocole "http(s)://"):
	domain_distant=www.monSite.com
	domain_local=127.0.0.1:80/test_makefile

	path_distant=~/monsite_com_prod/
	path_local=./

else
	error_custom=---- Erreur choix ENV ----
endif

#---------------------------------------------------------------
# COLOR
# ex: 	@echo "${_BOLD}${_UNDER}${_ICYAN}Hello World${_END}"
#---------------------------------------------------------------

# This is a minimal set of ANSI/VT100 color codes
_END=\x1b[0m
_BOLD=\x1b[1m
_UNDER=\x1b[4m
_REV=\x1b[7m

# Colors
_GREY=\x1b[30m
_RED=\x1b[31m
_GREEN=\x1b[32m
_YELLOW=\x1b[33m
_BLUE=\x1b[34m
_PURPLE=\x1b[35m
_CYAN=\x1b[36m
_WHITE=\x1b[37m

# Inverted, i.e. colored backgrounds
_IGREY=\x1b[40m
_IRED=\x1b[41m
_IGREEN=\x1b[42m
_IYELLOW=\x1b[43m
_IBLUE=\x1b[44m
_IPURPLE=\x1b[45m
_ICYAN=\x1b[46m
_IWHITE=\x1b[47m

#---------------------------------------------------------------
# FONCTION
#---------------------------------------------------------------

# Appeler la fonction : $(call message_primary, "Votre message ici")
# @param $(1) votre message ici
# @return string
define message_primary
	@echo -e "\n${_BOLD}${_BLUE}---- $1 ----${_END}"
endef

# Appeler la fonction : @echo $(call test_fonction_custom, lorem, ipsum)
#test_fonction_custom_2 = Variable Name: $(0) ---- First: $(1) ---- Second: $(2) ---- Empty Variable: $(3)

#---------------------------------------------------------------
# SCRIPT CONFIG (ne rien modifier ci-dessous)
#---------------------------------------------------------------

.PHONY: help config debug dev update install_local import dbimport deploy dbdeploy

help: ## Affiche cette aide
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

test: ## Test : utilisation de fonctions
	$(call test_fonction_custom_1, "loremA", "ipsumA")
	@echo $(call test_fonction_custom_2, lorem, ipsum)

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

test_color: ## Afficher : texte avec couleur (font or background)
	@echo -e "${_BOLD}${_GREY}Hello World${_END}"
	@echo -e "${_BOLD}${_GREEN}Hello World${_END}"
	@echo -e "${_BOLD}${_RED}Hello World${_END}"
	@echo -e "${_BOLD}${_YELLOW}Hello World${_END}"
	@echo -e "${_BOLD}${_BLUE}Hello World${_END}"
	@echo -e "${_BOLD}${_PURPLE}Hello World${_END}"
	@echo -e "${_BOLD}${_CYAN}Hello World${_END}"
	@echo -e "${_BOLD}${_WHITE}Hello World${_END}"

#---------------------------------------------------------------
# NEW INSTALL WP (en local)
#---------------------------------------------------------------
install_local: ## Installer une nouvelle version de Wordpress (en local)
	$(call message_primary, "DOWNLOAD WP")
	wp core download --locale=fr_FR --force

	$(call message_primary, "VERSION WP")
	wp core version

	$(call message_primary, "CONFIG DATABASE")
	wp core config \
		--dbhost=$(dbhost_local) \
		--dbname=$(dbname_local) \
		--dbuser=$(dbuser_local) \
		--dbpass=$(dbpass_local) \
		--dbprefix=$(dbprefix_local) \
		--locale=fr_FR \
		--skip-check

	$(call message_primary, "CREATE DB")
	wp db create

	$(call message_primary, "INSTALL WP")
	wp core install \
		--url=$(domain_local) \
		--title=Test \
		--admin_user=$(admin_user_local) \
		--admin_password=$(admin_password_local) \
		--admin_email=$(admin_email_local) \
		--skip-plugins=hello \
		--skip-themes=twentyfifteen,twentysixteen,twentyseventeen,twentynineteen,twentytwenty

	#$(call message_primary, "INSTALL PLUGINS")
	#wp plugin install jetpack --activate
	#wp plugin install contact-form-7 --activate
	#wp plugin install wordpress-seo --activate
	#wp plugin install updraftplus --activate
	#wp plugin install backwpup

	$(call message_primary, "INSTALL THEME")
	#wp theme install astra --activate
	wp theme install twentytwenty --activate

	$(call message_primary, "PAGES CREATE - accueil / blog / contact / mentions megales")
	wp post create --post_type=page --post_title='Accueil' --post_status=publish
	wp post create --post_type=page --post_title='Blog' --post_status=publish
	wp post create --post_type=page --post_title='Contact' --post_status=publish
	wp post create --post_type=page --post_title='Mentions Legales' --post_status=publish

	$(call message_primary, "ARTICLES CREATE : FAKER")
	curl -N http://loripsum.net/api/5 | wp post generate --post_content --count=5 --post_date=$(date -v-120M '+%Y-%m-%d-%H-%M-%S')

	$(call message_primary, "CONFIG SET PAGE - selectionner page accueil / article")
	wp option update show_on_front page
	wp option update page_on_front 4
	wp option update page_for_posts 5

	$(call message_primary, "CONFIG MENU")
	wp menu create "Menu Principal"
	wp menu item add-post menu-principal 3
	wp menu item add-post menu-principal 4
	wp menu item add-post menu-principal 5
	wp menu item add-post menu-principal 6

	$(call message_primary, "SUPPRIMER : plugin - theme - articles exemples")
	wp post delete 1 --force # supprime : article + commentaire
	wp post delete 2 --force # supprime : article + commentaire
	wp plugin delete hello
	wp theme delete twentytwenty
	wp theme delete twentynineteen
	wp theme delete twentyseventeen
	wp option update blogdescription ''

	$(call message_primary, "UTILISER PERMALIENS")
	wp option get permalink_structure
	wp option update permalink_structure '/%postname%'
	wp rewrite flush --hard

	$(call message_primary, "CATEG et TAG - update")
	wp option update category_base theme
	wp option update tag_base sujet

	$(call message_primary, "GIT - init")
	git init
	git add -A
	git commit -m "Initial commit" > /dev/null 2>&1

	$(call message_primary, "OPEN PAGES - accueil et admin")
	open "http://"$(domain_local)
	open "http://"$(domain_local)"/wp-admin"

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
	wp search-replace '$(domain_distant)' '$(domain_local)' --dry-run
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
