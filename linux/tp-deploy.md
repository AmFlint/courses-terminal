# TP: Déployez votre premier site web, sur un serveur

Dans ce TP, vous allez apprendre:
- Connexion à un serveur distant avec le protocole SSH (Secure SHell)
- Configurer un serveur web
- Automatiser le déploiement de votre site web

Cela vous permettra de mieux comprendre ce qu'il se passe sur netlify lorsque vous "pushez" sur la branche master.

**Utilisez votre projet de semaine intensive d'intégration**.

## Introduction

Dans ce TP, je vais vous fournir un serveur sur lequel vous pourrez vous connecter avec le protocole SSH pour l'administrer: Configurer NGINX, intéragir avec le système de fichier (vous créer un dossier), uploader le code source de votre site web, puis par la suite écrire un script d'automatisation du déploiement du site web.

## Pré-requis

- [Installer sshpass](https://gist.github.com/arunoda/7790979):
  - `sudo apt install sshpass` sur Linux
  - `brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb` sur MacOS

## Se connecter à un serveur distant avec SSH

Afin d'administrer un serveur distant, on peut utiliser le protocole SSH (Secure SHell), qui nous permet d'établir une session shell avec un ordinateur distant à travers le réseau. Une session Shell veut dire que l'on pourra se connecter au serveur, et lancer des commandes Shell (par exemple, cd, mkdir, cp, ls, rm, apt install, apt update, vim ...).

Pour ce faire, nous utiliserons la commande `ssh`, qui se décompose comme suit:
```bash
# <user>: l'utilisateur sur lequel on souhaite se connecter sur le serveur distant
# <host>: Hôte (adresse IP ou Nom de Domaine) du serveur
ssh <user>@<host>

# Avec les données de ce tp:
ssh tp@163.172.172.180
# Le serveur vous demandera ensuite le mot de passe, tapez le mdp et appuyez sur la touche "entrer"
tp@163.172.172.180's password:
```

**Très important pour la partie `automatisation`: On peut utiliser le flag `-t` pour passer une commande à effectuer, sans avoir à se connecter**:
```bash
# Ici, j'ouvre une connexion avec le serveur, je lance la commande ls /, et je ferme directement la connexion en une seule commande
ssh tp@163.172.172.180 -t ls /
```

Petit bémol de la commande `ssh`, le mot de passe vous sera demandé à nouveau, vous pouvez en revanche utiliser la commande `sshpass` pour contourner le problème (vous verrez dans les étapes à suivre, au niveau du script d'automatisation):
```bash
# sshpass permet de passer le mot de passe en argument, puis de se connecter au serveur et exécuter une commande. Tout cela en une seule commande
sshpass -p <le-mot-de-passe> ssh tp@163.172.172.180 -t ls /
```


## Configurer le serveur Web NGINX

Dans ce TP, un même serveur Linux est partagé entre tous les étudiants, je vous demanderai donc de bien vouloir:
- Créer un dossier appelé `nom-prénom` dans `/home/tp`, par exemple: `mkdir /home/tp/masselot-antoine`. Vous pourrez uploader votre code à l'intérieur de ce dossier.
- Créer un fichier de configuration pour `nginx` à l'emplacement `/etc/nginx/sites-enabled/nom-prénom`: par exemple `touch /etc.nginx/sites-enabled/masselot-antoine`. **Dans ce fichier, vous devrez impérativement définir les attributs**:
  - `root`: Pointe sur votre dossier (/home/tp/masselot-antoine)
  - `server_name`: Nom de domaine pour lequel la configuration sera appliquée. **Le server_name doit être sous le format `nom-prenom.mymutu.fr`,** par exemple `masselot-antoine.mymutu.fr`. Ensuite, notifiez moi pour que je puisse créer le nom de domaine en question.
  - `listen 80;`: écouter sur le port 80 du serveur
  - `index`: préciser le fichier `index.html`

**Pour vérifier que la configuration d'NGINX est correct avant de relancer le service, lancez la commande suivante**:
```bash
# -t pour "tester" la syntaxe des fichiers de configuration
# sudo pour exécuter la commande en tant qu'administrateur: root
sudo nginx -t
```

**N'oubliez pas de relancer le service nginx pour appliquer votre configuration** (uniquement lorsque vous modifiez la configuration nginx, pas à chaque modification du code du site):
```bash
sudo service nginx restart
```

## Uploader des fichiers de votre ordinateur vers un serveur distant

Maintenant, vous aurez besoins de prendre votre site, et de l'envoyer sur le serveur, pour ce faire, vous pourrez utiliser la commande `scp`:
```bash
# Commande générique
# -r permet d'uploader un dossier et son contenu, sans le -r, la commande scp cherche à uploader un seul fichier et ne fonctionnera pas avec un dossier (le dossier contenant votre site)
scp -r /chemin/vers/dossier/du/site <utilisateur>@<hôte>:/chemin/sur/instance

# La commande suivante est chargée d'uploader un dossier "dossier-de-mon-site"
# sur la machine avec l'IP 163.172.172.180 en tant qu'utilisateur "tp"
# à l'emplacement /home/tp/masselot-antoine
scp dossier-de-mon-site tp@163.172.172.180:/home/tp/masselot-antoine
# Évidemment, vous devrez taper votre mot de passe
```

Vérifiez bien que votre site a été copié au bon endroit sur le serveur, vous pourrez évidemment vérifier que le serveur web NGINX sert votre site correctement en vous connectant sur le site http://nom-prenom.mymutu.fr (remplacez nom-prenom par votre nom et prénom).

## Automatisation du déploiement du site

Une fois votre site en ligne, nous allons travailler sur un `script shell`, pour automatiser la mise à jours de celui ci sur le serveur, en une seule commande.

En gros, nous allons écrire une suite de commande dans un fichier, puis l'exécuter pour lancer toutes ces commandes. Parmis ces commandes, on voudra uploader le contenu de notre site (mis à jours) sur le serveur avec les commandes `sshpass` et `scp`.

Pour écrire un script Shell, il suffit simplement de créer un fichier (par convention, on utilisera l'extension `.sh`: shell):
```bash
# créer un fichier monscript.sh
touch monscript.sh
```

Vous pouvez évidemment créer ce fichier depuis votre éditeur de texte préféré.

À l'intérieur du fichier, on pourra écrire des commandes shells, par exemple:
```bash
ls /
mkdir test-script
touch test-script/test-fichier
```

Ici, ce script va:
- afficher les fichiers/dossiers dans le dossier `/` dans notre terminal
- créer un dossier `test-script`
- créer un fichier `test-fichier` dans le dossier `test-script`

Pour exécuter le script, on lancera la commande `bash`:
```bash
# Exécuter le fichier monscript.sh
bash monscript.sh
```

Pour cette partie, vous devrez créer le script shell dans le dossier contenant votre site web.

À l'exécution du script, le contenu de votre dossier (dans lequel se trouve votre site web) doit être uploadé sur le serveur donné, dans votre dossier personnel, ce qui mettra à jours le contenu de votre site web (puisque le serveur web va lire les fichiers présents dans votre dossier personnel).

## Bonus: Automatisation du déploiement au push sur Github

Il nous manque à présent une étape: exécuter le script de déploiement du site web, lorsque l'on push sur le projet sur Github. Après cette étape, nous aurons toute la chaîne effectuée par `Netlify`.

Pour cela, nous allons regarder les `Github Actions`, sorties très récemment: [Voir la documentation](https://github.com/features/actions).

+ à venir sur le sujet....
