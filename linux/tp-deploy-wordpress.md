# Déployer un site avec Wordpress

Dans le TP précédent, nous avons appris:
- à nous connecter à un serveur distant avec `SSH`
- à configurer nginx et utiliser les `virtual hosts` (server_name) pour utiliser un fichier de configuration nginx par nom de domaine
- Uploader des fichiers de notre ordinateur vers un serveur distant avec `scp`

Nous allons à présent travailler sur l'étape suivante: `déployer un site dynamique` (avec un langage serveur comme PHP, et une base de données MySQL).

Dans ce TP nous utiliserons les technologies suivantes:
- PHP
- MySQL (base de données)
- nginx (serveur web)
- Wordpress (Moteur de gestion de contenu écrit en PHP)

L'objectif est simple: installer et configurer Wordpress (avec nginx) sur un serveur Linux pour servir un site dynamique, développé en PHP et se connectant à une base de données MySQL.

## Installer les logiciels nécessaires

**Cette partie a déjà été effectuée par mes soins, car comme tous les étudiants partagent le même serveur Linux, j'ai du installer les différents logiciels au préalable, je vous laisse tout de même cette partie dans le TP, pour que vous puissiez la reproduire sur un serveur si vous le souhaitez**.

**Notez que l'installation des logiciels se fait sur le serveur avec SSH, et non pas sur votre ordinateur**.

### Installer un serveur web: NGINX

Installer le packet `nginx` sur votre serveur.

### Installer et configurer PHP

Vous aurez besoins d'installer les packages suivant:
- `php`
- `php-fpm`
- `php-mysql`

### Installer MySQL

Pour déployer une application `back-end`, nous aurons souvent besoins d'utiliser une base de données, à laquelle notre code (PHP, node.js, ruby, python, go ...) va se connecter pour effectuer des actions. Par exemple: Créer un utilisateur (comme un profil sur Facebook par exemple), puis gérer la connexion de l'utilisateur sur notre site: Demander à la base de données si username/mot-de-passe existent....

Pour cette première année, vous apprendrez à utiliser le moteur `MySQL` avec Aymeric Mayeux plus tard dans l'année. Dans ce TP, nous allons donc utiliser une base de données MySQL (et survoler certaines fonctionnalités car ce n'est pas l'objectif de mon cours) pour vous permettre de déployer un site sous Wordpress, qui utilise MySQL pour stocker des articles de blog (et pleins d'autres choses).

Nous pourrons donc installer deux packages:
- `mysql-server`: Le serveur de base de données MySQL, où nos bases seront hébergées. C'est sur cette application que notre site Wordpress va se connecter
- `mysql-client`: Le client MySQL, en gros un programme utilise avec des commandes `shell` (mysql) pour se connecter à un serveur MySQL (installé avec le package mysql-server) et administrer des bases.

## Télécharger Wordpress

Nous allons à présent télécharger le projet basique pour Wordpress, pour commencer à configurer notre site.

```bash
# wget envoie une requête HTTP à une adresse donnée, ici http://wordpress.org, et télécharge la réponse
# Dans ce cas, on va télécharger un fichier "latest.tar.gz" (tar.gz = archive compressée au format gzip)
wget -c http://wordpress.org/latest.tar.gz

# la commande tar permet de décompresser une archive (avec l'option -x pour extract pour extraire au format gzip)
tar -xzvf latest.tar.gz
```

Nous allons rencontrer un problème de permissions par la suite, car le dossier que vous venez de télécharger (contenant le code de base d'un site sous Wordpress) n'appartient pas à l'utilisateur défini par nginx (`www-data`). Lors de la première connexion à votre site, vous devrez configurer le site depuis votre navigateur, ce qui aura pour effet de créer des fichiers de configuration (que Wordpress pourra lire pour que votre site fonctionne correctement) pour la suite.

C'est l'occasion de pratiquer la partie `permissions` vue en cours, vous devrez donc:
- Changer de façon récursive (-R) le `owner` de votre dossier contenant le code Wordpress. Vous devrez changer l'owner pour `www-data` du groupe `www-data`.
- Changer les permissions sur le dossier téléchargé pour les suivantes:
  - L'utilisateur à qui appartient le dossier peut lire, écrire et exécuter
  - Le groupe d'utilisateurs à qui appartient le dossier peut lire et exécuter
  - Les autres utilisateurs peuvent lire et exécuter

Si vous ne changez pas ces permissions, vous ne pourrez pas configurer Wordpress depuis votre navigateur, car nginx va vouloir créer (`write`) des fichiers dans ce dossier, et comme il n'aura pas les permissions pour le faire, cela va échouer.

## Configurer le serveur web: nginx

Nous venons d'installer le code de notre site Wordpress, et d'accorder les permissions nécessaires sur le dossier pour que nginx puisse opérer correctement.

Nous allons à présent configurer nginx pour:
- Utiliser PHP pour lire le code php (Wordpress est écrit en PHP)
- Pointer la `root` du serveur Web sur le dossier dans lequel Wordpress est installé
- Ajouter un `server_name`, pour que votre configuration ne s'applique que pour votre nom de domaine. **Je vous ai crée des noms de domaine: `nom-prenom.wordpress.masselab.com` par exemple `masselot-antoine.wordpress.masselab.com`.**

Lorsque j'ai configuré le serveur avant le TP, j'ai configuré nginx pour venir chercher les fichiers de configurations dans votre session utilisateur dans un dossier `nginx`. Il faudra donc déposer votre fichier de configuration dans votre dossier `nginx`.

Par exemple, pour mon utilisateur `masselot-antoine`:
```bash
# Je me trouve dans /home/masselot-antoine
# Je rentre dans le dossier nginx
cd nginx

# Je copie le contenu du fichier "default" de la configuration nginx dans mon dossier, dans un nouveau fichier "masselot-antoine"
cp /etc/nginx/sites-enabled/default masselot-antoine

# Je peux à présent éditer ma configuration pour mettre à jours mes directives "root", "server_name", et "location ~\.php"
vim masselot-antoine
```

Ici, nous allons nous concentrer sur 3 directives:
- `server_name`: Le nom de domaine pour lequel votre configuration va répondre, `nom-prenom.wordpress.masselab.com`
- `root`: Le chemin dans le système de fichier dans lequel se trouve votre code Wordpress\
- `location ~\.php`: Lorsque l'on souhaite utiliser du code PHP derrière un serveur Web comme NGINX, on va utiliser cette directive pour indiquer à nginx comment interpréter le code PHP. (Dans le fichier default que vous avez certainement copié, la directive est commentée, il vous suffit de supprimer les "#").
```conf
# Cette partie du fichier de configuration doit être commentée, vous pouvez la décommenter et configurer la directive "fastcgi_pass unix:chemin/vers/socket/php"
location ~ \.php$ {
		include snippets/fastcgi-php.conf;
	  # With php-fpm (or other unix sockets):
		fastcgi_pass unix:/run/php/php7.2-fpm.sock;
	}
```

Comme vous le voyez ici, on utilise un système de socket, NGINX va se connecter à une socket située dans `/run/php/php7.2-fpm.sock` sur votre serveur pour envoyer le contenu des fichiers `.php`. Le processus php-fpm va ensuite exécuter le code, et passer la réponse à nginx, qui renverra la réponse au navigateur de l'utilisateur.

N'oubliez pas:
- de tester votre configuration avec la commande `sudo nginx -t`
- une fois le test correct, relancez le service `nginx`: `sudo service nginx reload`

Vérifiez ensuite que votre site Wordpress s'ouvre dans votre navigateur sur votre nom de domaine `http://nom-prenom.wordpress.masselab.com`, par exemple `http://masselot-antoine.wordpress.masselab.com`.

Si tout est bon, vous devriez voir une page d'installation de Wordpress, vous allez donc devoir passer à la configuration de MySQL, car Wordpress à besoins d'une base de données pour fonctionner

## Configurer une base de données MySQL

**Étant donné que vous n'avez pas encore reçu vos premiers cours de Back: PHP et base de données avec MySQL, j'ai effectué cette partie du TP pour vous, je laisse les indications afin que vous puissiez revenir sur le TP plus tard, pour prendre en main la configuration de la base de données, et la gestion des utilisateurs**.

**Passez donc à l'étape suivante, lorsque vous vous connecterez à votre site Wordpress pour la dernière fois vous devrez renseignerles identifiants de connexion à la base, à savoir**:
- Utilisateur: `nom-prénom` (ex: `masselot-antoine`)
- Nom de la base: `nom-prénom` (ex: `masselot-antoine`)
- mot de passe: `hetic`
- Hôte: `localhost`

Si vous continuez de lire cette partie c'est que vous avez votre propre serveur/instance, et que vous souhaitez configurer la base de données MySQL que votre site Wordpress va utiliser.

Pour cela, nous allons suivre 3 étapes:
- Créer une base de données
- Créer un utilisateur qui accèdera à cette base
- Donner les permissions à l'utilisateur crée précédemment d'accéder à la base crée plus haut.

Dans un premier temps, vous devrez initialiser une connexion à la base de données, afin de l'administer. Pour cela, on peut utiliser la commande `sudo mysql` (la commande mysql vient du package `mysql-client` que l'on a installé plus tôt):
```bash
# Vous devez être connecté au serveur avec SSH
sudo mysql
```

À présent, vous devez vous trouver dans le programme d'administration de `mysql-client`.
Vous pouvez créer une base de données avec la commande:
```sql
CREATE DATABASE "nomdelabase";
-- Si vous avez tapé la commande correctement vous devriez obtenir le résultat suivant:
Query OK, 1 row affected (0.00 sec)
```

Seconde étape: Créer un utilisateur MySQL, on peut faire cela avec la commande MySQL `CREATE USER`:
```sql
CREATE USER "nomduuser";
-- Si vous avez tapé la commande correctement vous devriez obtenir le résultat suivant:
Query OK, 0 rows affected (0.00 sec)
```

Nous avons à présent crée un utilisateur MySQL, et une base de données. Cependant, l'utilisateur ne possède aucune permission, il ne peut intéragir avec aucune base, et nous allons devoir lui donner manuellement des permissions.

On utilise la commande `GRANT ALL PRIVILEGES ON "nom-db".* TO "nomutilisateur"`:
```sql
GRANT ALL PRIVILEGES ON nomdelabase.* TO nomdeuser;
-- Si vous avez tapé la commande correctement vous devriez obtenir le résultat suivant:
Query OK, 0 rows affected (0.00 sec)
```

Nous venons de créer une base de données et un utilisateur MySQL. Nous avons ensuite donné tous les privilèges à l'utilisateur sur la base de données.

Nous pourrons donc utiliser cet utilisateur et cette base de données dans notre application PHP (Wordpress).

## Finaliser l'installation de Wordpress

Retournez dans votre navigateur sur votre site, nous allons pouvoir finaliser la configuration de Wordpress.

Lorsque vous suivez le guide d'installation, on vous demandera les identifiants de connexion à la base de données. Vous pourrez donc utiliser:
- Utilisateur: `nom-prénom` (ex: `masselot-antoine`)
- Nom de la base: `nom-prénom` (ex: `masselot-antoine`)
- mot de passe: `hetic`
- Hôte: `localhost`

Cliquez sur `suivant`, choisissez un utilisateur pour administer le site (sera votre compte pour vous connecter à l'interface de gestion du contenu de votre site plus tard).

Une fois l'installation terminée, votre site est à présent en ligne.
