# Gérer des serveurs virtuels avec Amazon Web Services

Ce TP constitue le dernier cours/exercice en système en Web 1.

Nous avons vu comment installer et configurer des logiciels comme NGINX et PHP-FPM sur un server déjà crée et configuré. Aujourd'hui, nous allons voir comment créer et configurer un serveur (`Machine Virtuelle` ou `Instance` dans le jargon du Cloud), pour héberger des sites web dessus (comme on a pu le voir lors des TPs précédents).

Ici, nous allons nous baser sur le Cloud Provider `Amazon Web Services` puisqu'il propose un système de `Free Tier` permettant aux étudiants de se former gratuitement sur leurs solutions.

Assurez vous d'avoir [créer un compte sur la console AWS](console.aws.amazon.com) avant toute chose.

Cela vous permettra de vous approprier les `bases` de la mise en ligne d'un application web sur un serveur Linux, la complexité (le début) liée à la gestion de cette infrastructure.

## Pré-requis

- Créez un compte sur [AWS](https://aws.amazon.com/fr/).
- [Installez Docker sur votre ordinateur](http://github.com/AmFlint/courses-docker/tree/master/tp-install-docker)

## Installer et configurer un serveur Linux

Dans cet exercice, nous apprendrons à travailler sur Linux (ici, Ubuntu), sur la plateforme AWS (Amazon Web Services).

### Préparer le terrain: connexion avec `SSH`

Pour se connecter à un serveur distant, il faut utiliser la commande SSH (`Secure SHell`), qui nous permet d'initialiser une session Shell sur un ordinateur à distance (ici, notre machine virtuelle dans un Data Center).

Pour se connecter à une instance distante, SSH utilise un système de clés:
- Une clé publique, que l'on va renseigner à notre serveur (ici, à AWS qui va installer la clé sur le serveur)
- Une clé privée, que l'on doit garder secrète (uniquement sur notre ordinateur), et que l'on va utiliser pour se connecter à un serveur distant.

- Votre ordinateur va se connecter sur le port 22 (par défaut) à un serveur distant: `Voici ma clé privée`
- Le serveur va vérifier qu'il possède une clé publique, liée à la clé privée reçue
  - Si aucune clé ne correspond: `connexion refusée`
  - Si une clé correspond: `connexion acceptée`, vous êtes connectés au serveur.

Pour générer une pair de clé (toujours par paire: privée et publique), on utilise la commande `ssh-keygen`:
```bash
# -t => type d'algorythme utilisé pour générer la clé, ici on utilise RSA
# -b => Taille en bytes/octets de la clé => Ici 4 Ko
ssh-keygen -t rsa -b 4096
```
La commande ssh-keygen va vous poser plusieurs question, notamment l'emplacement sur votre système où vous souhaitez installer la clé, le mot de passe que vous souhaitez utiliser (facultatif) etc...

Par exemple:
```bash
➜  ~ ssh-keygen -t rsa -b 4096

Generating public/private rsa key pair.
Enter file in which to save the key (/Users/antoinemasselot/.ssh/id_rsa): /Users/antoinemasselot/.ssh/test_hetic_w3
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/antoinemasselot/.ssh/test_hetic_w3.
Your public key has been saved in /Users/antoinemasselot/.ssh/test_hetic_w3.pub.
The key fingerprint is:
SHA256:vx9Jt2uN/C5uTQTr6DFmMzRQc5otD9i7YNXP4TfzNKA antoinemasselot@Antoines-MacBook-Pro-2.local
The key's randomart image is:
+---[RSA 4096]----+
|           .o .  |
|          .o B.  |
|          ..B.o+ |
|           .+=+oo|
|        S oEo+o*=|
|         o oO+.oB|
|          .++*.=.|
|           ...*.o|
|          ...+o+o|
+----[SHA256]-----+
```
J'ai généré une paire de clé à l'emplacement `/Users/antoinemasselot/.ssh/test_hetic_w3`, sans mot de passe, et j'ai obtenu deux nouveaux fichiers:
- /Users/antoinemasselot/.ssh/test_hetic_w3.pub => `clé publique`
- /Users/antoinemasselot/.ssh/test_hetic_w3 => `clé privée`

Ensuite, nous allons devoir renseigner le contenu la clé `publique` (.pub), à notre Provider (ici AWS), afin de pouvoir utiliser la clé privée, pour s'identifier à notre serveur plus tard dans le TP.

Pour ce faire, connectez vous sur la console AWS, choisissez le service `EC2`, et déplacez vous dans l'onglet `Key Pair` ou `Paire de clés` dans le menu à gauche.

Vous pourrez importer une clé (bouton en haut à droite), c'est à dire télécharger le contenu de votre fichier.

**Partie à effectuer pour le TP**:
- Générer une paire de clé (`ssh-keygen`), appelez-la comme vous souhaitez
- Copier le contenu de la clé publique (fichier avec l'extension `.pub`)
- Allez sur l'interface `EC2 Import Key Pair` d'AWS pour y importer votre clé publique

Une fois cette partie correctement effectuée, nous allons passer au démarrage du serveur.

### Configuration réseau

Ici, nous allons voir une partie importante concernant la sécurité de notre instance et des services exposés dessus à l'avenir. Nous allons utilisé les `groupes de sécurité`, qui permettent de configurer le `Firewall/pare-feu` de l'instance. Cela nous permettra de choisir minutieusement quels ports de l'instance seront exposés pour quelles addresses.

Typiquement, pour laisser l'accès à un site Web (`HTTP` sur les ports 80 et 443 pour `HTTPS`) exposé sur une instance, nous allons autoriser toutes les addresses IP (`0.0.0.0`) à accéder aux ports `80` et `443` avec le protocole `TCP`.

Voici donc les instructions:
- Créez un nouveau groupe de sécurité, appelez-le `tp-aws`
- Le port 22 doit être accessible (pour le SSH)

### Création de l'instance

Maintenant que nous avons configuré les différentes ressources qui vont nous permettre de travailler sur cette instance, nous allons pouvoir passer à la partie Machine Virtuelle:
- Allez dans l'onglet `Instances` de la console EC2.
- Créez une nouvelle instance
- Sélectionnez l'OS `Ubuntu 18.04`, puis le type `t2.micro`, cela vous permettra d'effectuer le TP sans dépenser d'argent.
- Configurez la section réseau pour utiliser le `Groupe de Sécurité` crée précédemment
- Validez la création de l'instance, pensez à choisir la clé `tp-aws` crée précédemment.
- L'instance devrai à présent être en cours de création, nous allons pouvoir nous y connecté, une fois l'adresse IP assignée. Veuillez Copier l'adresse IP de votre instance:
  ![console ec2](./assets/ec2-create.png)

<!-- ### Démarrer un serveur

Ici, nous allons apprendre à configurer et démarrer une Machine Virtuelle (VM) dans le Cloud.

**Partie à effectuer pour le TP**:
- Dans l'onglet `Instances`, cliquez sur le bouton `Create an Instance`
- Pour la configuration:
  - Sélectionnez l'image `Ubuntu`: Système d'exploitation installé sur votre instance
  - Sélectionnez `Paris` comme région: Il s'agit du pays où votre instance sera démarée
  - Sélectionnez le type d'instance `DEV1-S` (onglet `Development`), il s'agit de la puissance de votre serveur (CPU, Mémoire et Stockage)
  - Ne touchez pas la partie `Volumes`, il s'agit du stockage alloué à notre serveur
  - Créez votre instance (en bas).

Une fois l'instance prête, une `adresse IP` est assignée (**Public IP**):
![instance info](./assets/instance_info.png)

Vous pouvez ensuite copier votre IP, vous rendre dans votre terminal et vous connecter au serveur avec la commande SSH, que nous allons voir dans le prochain point. -->

### Se connecter à un serveur distant avec `SSH`

Rappelons les étapes précédentes:
- Création d'une paire de clés SSH
- Renseignement de la clé PUBLIQUE générée précédemment dans la console AWS
- Création d'une instance, qui utilise la clé générée.

Nous allons maintenant pouvoir nous connecter à notre instance avec la commande `ssh`:
```bash
# -i => Chemin vers le fichier comprenant la clé privée
# <user>: l'utilisateur sur lequel on souhaite se connecter sur le serveur distant
# <host>: Hôte (adresse IP ou Nom de Domaine) du serveur
ssh -i /chemin/vers/la/clé/privée <user>@<host>

# Exemple, avec les données du cours ci-dessus:
ssh -i /Users/antoinemasselot/.ssh/test_hetic_w3 ubuntu@34.252.212.40
```

**Partie à effectuer pour ce TP**:
- Utilisez la commande `ssh` pour vous connecter à votre serveur distant:
  - `-i` pour préciser le chemin vers la clé que vous avez crée précédemment
  - `ubuntu`: L'utilisateur sur le serveur sur lequel vous devez vous connecter. Par défaut, AWS nous créer un utilisateur `ubuntu`, sur lequel nous pouvons nous connecter pour administrer le serveur.
  - `adresse IP de votre serveur`: utilisez l'adresse IP de votre instance, renseignée dans les informations après avoir crée votre serveur.

### Installer les logiciels/librairies nécessaires

Une fois le serveur démarré, il nous faut ensuite installer les logiciels nécessaire pour exécuter nos applications. Par exemple, si nous souhaitons héberger une application écrite en `ruby`, il faudra installer le packet `ruby`.

Dans ce TP, nous utiliserons Docker, puisque nous avons travaillé dessus lors d'une précédente formation.

Pour installer des packages sur un système, on utilise un [Package Manager](https://fr.wikipedia.org/wiki/Gestionnaire_de_paquets) (par exemple, npm et composer sont des gestionnaires de packets). Chaque système d'exploitation possède son propre gestionnaire de packet:
- Chocolatey pour Windows
- HomeBrew pour MacOS
- APT pour Ubuntu et Debien
- Yum pour Red Hat Enterprise Linux et CentOS
- ...

Ici, nous avons installé un système d'exploitation `Ubuntu`, nous utiliserons donc la commande `apt` pour installer nos packages:
```bash
# Mettre à jours le registre local, pour que notre système sache quel packet on peut installer et comment l'installer
apt update
# Ensuite, on peut installer un package avec la commande install
apt install <nom-du-packet>
```

**Notez que ces commandes doivent être exécutées en tant qu'administrateur (root, sudo sur Linux)**, il faudra donc préfixer les commandes `apt` par la commande `sudo` pour exécuter `apt` en tant qu'administrateur.

Pour obtenir des informations sur un package, et notamment comment l'installer, on va se référer à la documentation du packet, très simplement: `rechercher "install nodejs ubuntu" sur google`.

Par exemple, pour [installer node.js et npm sur Ubuntu](https://doc.ubuntu-fr.org/nodejs):
```bash
sudo apt install nodejs npm
```

Comme dit précédemment, nous installerons [Docker ici, en suivant la documentation officielle](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

**Partie à effectuer pour le TP**:
- Installer php et php-fpm
- Installer nginx
- Installer mysql-client et mysql-server


## Pour la suite

Maintenant que vous avez configuré votre serveur Linux, et installé les logiciels nécessaire pour déployer vos premiers sites Web, je vous demanderai de repasser sur les différentes TPs vus précédemment en cours, en appliquant cette fois une logique différente pour effectuer les exercices sur VOTRE serveur, et non plus sur celui que je vous ai fournis:
- [Déployer un site Statique (+ bonus refaire netlify maison)](./tp-deploy.md)
- [Déployer un site Dynamique avec PHP, MySQL, Nginx et WordPress](./tp-deploy-wordpress.md)

**Pour les plus avancés, vous pouvez vous orienter sur ces différents TPs, effectués en Web 2:**
- [TP Déployer app front+back sur une seule instance AWS](./tp-holidays)
- [TP Gérer un parc de serveurs pour déployer une application Front-Back sur AWS](https://github.com/AmFlint/courses-infrahosting/tree/master/tp-aws-ec2) (+ difficile)