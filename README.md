# shopinvader-documentation

Installation d'un environnement Shopinvader avec LocomotiveCMS et Odoo 


Sommaire
=================

1) Création d'un nouveau container
2) Paramétrage d'un nom de domaine 
3) Installation de Ruby On Rails
4) Installation de MongoDB
5) Installation de Node.js
6) Installation de LocomotiveCMS
7) Lancement du serveur LocomotiveCMS
8) Clonage d’un template Shopinvader
9) Déploiement de la boutique Shopinvader




1)Création d’un nouveau container
=================

Le but de cette étape est de créer un nouveau container qui vas accueillir notre LocomotiveCMS
afin de le rendre disponible sous un nom de domaine 

→ Se rendre sur un site Registrar (Bureau d’enregistrement de noms de domaine) afin de réserver nouveau nom de domaine pour notre container


2)Paramétrer le nom de domaine
=================

Toujours sur notre site Registrar, lors de la création du container plusieurs informations vont être demandée (à compléter plus tard)


3)Installation de Ruby On Rails
=================

Le but de cette étape et de l’étape 4 et 5 sont de pouvoir installer LocomotiveCMS proprement. 

Se rendre dans notre nouveau container tout frais créé avec la commande :
```
	ssh root@’votre_nom_d’hôte’
```

‘ssh’ correspond au type de connexion 
‘root’ correspond à l’utilisateur avec lequel on souhaite se connecter
‘shop’ correspond à notre nom de domaine pour ce container
‘shop.saaslys.com’ correspond à l’hôte de notre container 

Certaines manipulation seront impossible avec l’utilisateur ‘root’, c’est pourquoi il est conseillé dès maintenant de se loguer avec l’utilisateur actuel avec la commande :
```
sudo su - ‘votre_nom_utilisateur’
```

Le Terminal devrait vous rendre la main sous cette forme :
```
‘votre_nom_utilisateur’@shop:~$
```

Effectuer un pwd afin de déterminer le chemin dans lequel on se trouve, normalement on se trouve à la racine de notre container, effectuer les différentes étapes dans l’ordre :

a. Installation de rbenv et des Dépendances 
--------------

Dans un premier temps, on met à jours la liste des paquets :
```
sudo apt update 
```
Ensuite, on installe les dépendances requises pour Ruby :
```
sudo apt install autoconf bison build-essential libssl-dev libyaml-dev 	libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm5 libgdbm-	dev
```

Une fois les dépendances installées, on peux installer rbenv lui même, il faut cloner le dépôt github suivant et l’installation s’effectuera automatiquement 
```
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
```
Ou ‘~/’ correspond au chemin ou l’on souhaite installer notre dossier .rbenv

Ensuite, il faut ajouter notre nouveau dossier .rbenv dans le chemin du fichier ~/.bashrc afin de pouvoir utiliser les commandes de rbenv :
```
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
```
la commande echo permet d’écrire la ligne 'export PATH="$HOME/.rbenv/bin:$PATH"' dans le fichier .bashrc

Faire de même avec la commande :
```
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
```
appliquer les changements effectués sur le fichier avec la commande :
```
source ~/.bashrc
```
lancer la commande 
```
type rbenv 
```
vas permettre de vérifier le contenu de notre fichier et voir si les lignes ont bien été écrites, le résultat devrait ressembler à ça :
```
rbenv is a function
rbenv ()
{
    local command;
    command="${1:-}";
    if [ "$#" -gt 0 ]; then
        shift;
    fi;
    case "$command" in
        rehash | shell)
            eval "$(rbenv "sh-$command" "$@")"
        ;;
        *)
            command rbenv "$command" "$@"
        ;;
    esac
}
```
Ensuite, il nous faut installer le plugin ruby-build, ce plugin vas ajouter la commande rbenv install, qui simplifie le processus d’installation pour les nouvelles versions de Ruby 

A ce stade, nous avons installé rbenv et ruby-build, installons maintenant Ruby. 


b. Installation de Ruby avec ruby-build
--------------

Lancer la commande suivante permettra de voir la liste complète des versions de Ruby disponible 
```
rbenv install -l 
```

Le résultat de cette commande devrait être une longue liste parmi laquelle on retrouvera une version 2.5.2, c’est celle ci qui nous intéresse. 

Pour l’installer, on lance :
```
rbenv install 2.5.2
```

Et pour définir cette version comme celle par défaut on lance :
```
rbenv global 2.5.2
```

On vérifie que la version installée et par défaut est bien la bonne avec :

	ruby -v

Le résultat devrait ressembler à ça :

	ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-linux]

c. Travailler avec les Gems
--------------

Les gems sont les processus et programmes nécessaires au bon fonctionnement de notre produit.

Dans un premier temps, on copie ce paramètre dans le fichier ~/.gemrc

	echo "gem: --no-document" > ~/.gemrc

On installe Bundle, un gestionnaire de Gem très efficace qui vas nous simplifier la tâche :

	gem install bundler 

Le résultat devrait ressembler à ça :

	Fetching: bundler-1.16.2.gem (100%)
	Successfully installed bundler-1.16.2
	1 gem installed
    
On peut exécuter la commande 

	gem env home 

pour vérifier notre environnement et notre configuration des gems, le résultat devrait ressembler à ça :

	/home/sammy/.rbenv/versions/2.5.1/lib/ruby/gems/2.5.0

    
d. Installation de Rails
--------------

On installe Rails avec la commande : 

	gem install rails 

Le résultat ressemble à ceci :

	Successfully installed rails-5.2.0
	38 gems installed
Suivi d’un :

	rbenv rebash 

Vérifier que Rails à bien été installé avec la commande :

	rails -v 

Si l’installation s’est bien déroulée, on devrait obtenir ceci : 

	Rails 5.2.0


4)Installation de MongoDB
=================

Faire un apt update afin de mettre à jour la liste de nos paquets si cette commande n’a pas été effectuée précédemment. 

Installer Mongodb :

	sudo apt install -y mongodb 

Cette commande vas vous installer la dernière version stable de mongodb, pour cette installation nous avons besoin d’une version 3.4

On vérifie ensuite le statut de notre service : 

	sudo systemctl status mongodb

Le résultat devrait ressembler à ça :

    mongodb.service - An object/document-oriented database
    Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor preset: enabled)
    Active: active (running) since Sat 2018-05-26 07:48:04 UTC; 2min 17s ago
     Docs: man:mongod(1)
    Main PID: 2312 (mongod)
    Tasks: 23 (limit: 1153)
    CGroup: /system.slice/mongodb.service
           └─2312 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config /etc/mongodb.conf


5)Installation de Node.js
=================

Installer Node.js et npm en une seule commande :

	sudo apt install nodejs npm

On vérifie la version avec 

	nodejs -v 


6)Installation de LocomotiveCMS
=================

Maintenant que tout nos outils sont installés, nous pouvons commencer la création d’une nouvelle application Ruby On Rails avec la commande :

	rails new locomotiveapp --skip-bundle --skip-active-record

(Si les permissions ne sont pas données, créer le dossier avec la commande mkdir en tant que root, changer le owner du dossier avec 

    chown www-data:antoine locomotiveapp2
    
et les droits avec   

    chmod -R 775 locomotiveapp2

On se rend dans notre nouveau dossier :

	cd locomotiveapp

On édite le fichier Gemfile avec la commande :

	vi Gemfile

Si la commande n’est pas reconnue, faire un 

	apt-get install vim

Pour installer l’éditeur de texte Vim.

Copier les lignes ci-dessous dans le fichier Gemfile :

Cliquer sur ‘i’ pour passer en mode Insertion 
Copier le contenu 
Quitter le mode Insertion avec ‘Echap’ 
Sauvegarder les modifications avec ‘ :’ + ‘x’ 


    source 'https://rubygems.org'

    gem 'devise', '~> 4.4.3'
    gem 'mongoid', '~> 6.4.0'
    # Use https for github source
    git_source(:github) do |repo_name|
        repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
        "https://github.com/#{repo_name}.git"
    end

    # Turbolinks makes following links in your web application faster. Read more: https://github.com/rails/turbolinks
    gem 'turbolinks'

    # needed for this rails application in order to build the js and css of locomotive engine (backend of the cms)
    gem 'coffee-rails'
    gem 'sass-rails'

    # Use puma for multi worker / multi process https://github.com/puma/puma
    gem 'puma', '~> 3.11.4'
    
    # Shopinvader / Locomotive gems use master branch for now
    gem 'locomotivecms', github: 'locomotivecms/engine', branch: 'master', ref: '9ed6083'
    gem 'locomotivecms_steam', github: 'akretion/steam', branch: 'v1.4.x.shopinvader'
    gem 'shop_invader', github: 'akretion/locomotive_shopinvader', branch: 'v3.4.x'
    gem 'mongo_session_store', '~> 3.1.0'

    group :development do
        # Access an IRB console on exception pages or by using <%= console %> in views
        gem 'web-console', '~> 2.0'
        gem 'byebug'

    # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails           /spring
        gem 'spring'
    end

    group :production do
        gem 'tzinfo-data'
        gem 'carrierwave-aws'
        gem 'rails_12factor'
        gem "sentry-raven"
    end


Une fois ce contenu copier, lancer l’installation du Bundle :

	bundle install 

Puis la génération des fichiers de config pour Mongodb et Devise avec les commandes :


	rails generate mongoid:config
	rails generate devise:install 

Faire un 

	bundle update 

Suivi d’un

	bundle exec rails generate locomotive:install 

Un second 

	bundle install

est requis. 


7)Lancement du serveur LocomotiveCMS
=================

Et Enfin, lancer le serveur Rails avec la commande : 

	bundle exec rails server

Notre Locomotive sera disponible sur l’url suivante : 
https(http)://’le_nom_de_votre_hôte’/locomotive 
Le site devrait renvoyer la page Register de LocomotiveCMS, on voous demande votre Nom, votre Mail, votre Mot de passe 

Une fois enregistré, nous pouvons créer un nouveau site, seul un nom de site est demandé, la 'Handle' est un identifiant unique permettant de ciblé le bon site dans les fichiers de configurtion, vous pouvez le modifier mais ce n'est pas recommandé. 

Nous somme ensuite redirigé sur la page principale de configuration de notre CMS, Seul les onglets Dashboard, Pages, Models, Assets, General Settings et Developers sont présents pour le moment. 


8)Clonage d’un template Shopinvader
=================

Maintenant que votre LocomotiveCMS est opérationnel, il nous faut un template afin d’obtenir le rendu de la boutique côté client. 


Créer un nouveau dossier sur le PC en local, et cloner le template de base de Shopinvader avec la commande : 

	git clone https://github.com/akretion/shopinvader-template.git YOUR_SITE_NAME

Le dépôt actuel ne contient aucun Gemfile, il faut donc en ajouter un manuellement, créer un nouveau fichier appelé Gemfile et copier les lignes suivantes à l’intérieur :

    source 'https://rubygems.org'

    # Use https for github source
    git_source(:github) do |repo_name|
        repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
        "https://github.com/#{repo_name}.git"
    end

    gem 'locomotivecms_wagon', '~> 2.4.0'
    gem 'locomotivecms_steam', github: 'akretion/steam', branch: 'v1.4.x.shopinvader'


    group :misc do
        gem 'shop_invader', github: 'akretion/locomotive_shopinvader', branch: 'v3.4.x'
    end

    gem 'byebug'
    gem 'guard-livereload', '~> 2.5.1'

Maintenant déployons notre site en local afin d’obtenir un aperçu du template : 

	cd YOUR_SITE_NAME

	docker-compose run --service-ports wagon gosu ubuntu bash
    
Une fois dans le wagon, lancer la commande : 

	wagon serve

Votre site sera disponible localement sur l’adresse http://0.0.0.0:3333

Déployons maintenant notre site en ligne sur notre serveur Locomotive :

Nous aurons besoin de nous rendre dans l’onglet Developer de LocomotiveCMS afin de récupérer les informations concernant la configuration de notre site, nous aurons besoin des informations suivantes : 

production : 
    host: ...
    handle: ...
    email: ...
    api_key: ...
    
    
Saisir les informations suivantes dans le fichier deploy.yml. 

Effectuer ensuite les commandes suivantes pour envoyer votre site sur le serveur Locomotive : 

	bundle exec wagon deploy production

Où ‘production’ est le nom que l’on à donné à notre service de déploiement. 

Notre boutique est maintenant accessible sur l’url suivante :

	https(http)://’le_nom_de_votre_hôte’


















