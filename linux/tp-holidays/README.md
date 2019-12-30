# TP - Mettre un site en ligne sur une instance AWS EC2

La stack technique:
- 1 Application Front-End écrite avec React
- 1 Application Back-End, écrite avec node.js
- 1 Base de Données MySQL

L'aspect fonctionnel:
- L'application Front-End va s'afficher dans le navigateur de l'utilisateur (fichiers html/css/js servis par un serveur web NGINX sur le serveur Linux)
- L'application Back-End va tourner sur le serveur Linux (avec le logiciel node.js), il va se connecter à une base de données MySQL pour stocker une liste de messages, de façon dynamique
- L'application Front-End va se connecter à l'application Back-End pour recevoir la liste de messages, et l'afficher dans l'interface graphique

Dans ce TP, vous allez:
- Installer des packets système (node, mysql, nginx)
- Configurer une base de données, un utilisateur + permissions MySQL
- Configurer et lancer une application back-end
- Configurer notre propre service Linux pour lancer l'application back-end
- Configurer, builder et servir une application Front-End avec React derrière un serveur Web Nginx.


**Au moment d'écriture de ce TP, les étudiants de Web1 n'ont pas encore été introduit au développement Back-End... Et vont donc survoler certaines notions**, cependant voici des ressources pour prendre de l'avance et vous renseigner sur les différentes technos utilisées dans cet exercice:
- [Base du backend avec node.js et Express.js](https://www.youtube.com/watch?v=L72fhGm1tfE)
- [Bases de MySQL](https://www.grafikart.fr/formations/mysql)


## Exercice

Nous allons à présent déployer une application avec les composants suivants:
- Base de donnée MySQL, contenant une table `messages` (`id`, `content`) pour stocker des messages.
- API Back-end développée avec node.js et express.js, intéragit avec la base pour lire la liste de messages.
- Front-end développé avec React, intégit avec l'API pour afficher la liste de message dans une interface graphique

### MySQL

Vous pouvez installer `MySQL` avec les packets suivants:
- `mysql-server`
- `mysql-client`

Je vous invite à lire les documentations suivantes pour installer les différents composants nécessaires sur votre instance:
- [MySQL](https://doc.ubuntu-fr.org/mysql)

**Pour des raisons de sécurité, nous allons créer un nouvel utilisateur pour la base de donnée, en ajustant ses permissions, pour lui donner les droits que sur une base**, [référez-vous à la documentation sur comment créer un utilisateur et lui allouer des permissions](https://linuxize.com/post/how-to-create-mysql-user-accounts-and-grant-privileges/) (ou au TP précédent sur WordPress):
- Créez une base de donnée `infra`
- Créez un utilisateur du nom de `infrauser` avec le password `infrapassword`
- Donnez les permissions (all privileges) à cet utilisateur sur la base `infra`
- À présent nous utiliserons cette utilisateur pour intéragir avec la base.

Une fois MySQL installé sur votre instance, vous pouvez utiliser [ce fichier database.sql](./database.sql) contenant la structure de la base de données à utiliser pour cette exercice. Pour ce faire, vous devrez d'abord uploader le fichier database.sql sur votre instance Database avec la commande `scp` que nous avons vu précédemment.

Par la suite, il faudra importer la structure décrite dans le fichier database.sql dans la base de données, à l'aide de la commande `mysql` sur l'instance directement (Utilisez votre nouvel utilisateur):
```bash
# -u => préciser le nom d'utilisateur pour se connecter
# -p => affichera un petit prompt "Enter password" quand vous lancerez la commande, il faudra indiquer le mot de passe de votre utilisateur
# < database.sql => Importer le contenu du fichier database.sql dans la base de donnée <database-name>
mysql -u <user> -p <database-name> < database.sql

# exemple
# Je me connecte en tant que l'utilisateur masselot sur la base de donnée "mabase", et j'importe le fichier database.sql
# Mon mot de passe sera demandé lorsque j'exécuterai la commande
mysql -u masselot -p mabase < database.sql
```
Cette commande aura pour effet d'importer le fichier database.sql dans la base de donnée sélectionnée, en se connectant avec les identifiants `user` et `password`.

**Une fois la base de données initialisée, pensez à configurer votre API avec les différentes variables d'environnements listées au-dessus, puis relancer le serveur node.**

### Application Back-end

Côté configuration, l'application utilise les variables d'environnement suivantes:
- `PORT`: Défini sur quel port de la machine le serveur va écouter
- `DB_HOST`: L'hôte du service MySQL (ou adresse IP)
- `DB_USER`: Utilisateur de la base de donnée (**n'utilisez pas root**)
- `DB_PASSWORD`: Le mot de passe de la base de donnée
- `DB_NAME`: Nom de la base de donnée

Les variables d'environnement nous permettent d'écrire une seule fois le code et le ré-utiliser dans plusieurs environnement très simplement, en exportant ces variables sur le serveur, plutôt que de changer le code pour par exemple changer le nom de l'utilisateur MySQL.

**Petit rappel: Pour créer une variable d'environnement utilisable dans une application, on utilise la commande export, comme par exemple**:
```bash
# Créer la variable d'environnement PORT avec la valeur 3000
export PORT=3000

# Donc pour la configuration spécifiée au-dessus, il faudra définir chacune des variables
# Votre Database tourne en local sur le même ordinateur, vous pouvez donc utiliser 127.0.0.1 ou localhost comme HOST
# Pour le reste, il s'agit des données utilisées dans la partie précédente sur MySQL, infrauser, infrapassword ...
export DB_HOST=127.0.0.1
export DB_USER=...
export DB_PASSWORD=...
```
Pour lister les variables d'environnements configurées, on utilise la commande suivante:
```bash
env
```

Vous aurez besoins [d'installer nodejs et npm](https://doc.ubuntu-fr.org/nodejs) sur votre instance pour lancer l'API (écrite en node).

Par la suite, vous devrez uploader [le code de l'api présent ici](./server) sur l'instance à l'aide de la commande `scp` (fiez vous au TP précédent).

Une fois le code sur l'instance, pensez à vous placer dans le dossier de l'API avec cd, puis installez les dépendances de l'application avec `npm install`, vous pourrez lancer l'application avec la commande `npm run start`.

Pour vérifier que l'application Back-End fonctionne correctement, envoyez une requête HTTP:
-  à l'aide de la commande `curl` sur l'API (en étant connecté au serveur):
    ```bash
    curl 127.0.0.1:3000
    ```
    Vous devriez obtenir le résultat suivant:
    ```json
    {
      "message": "hello"
    }
    ```
    Vérifiez également que votre application arrive à se connecter à votre base de données MySQL correctement:
    ```bash
    # curl envoie une requête HTTP à un ordinateur (IP:PORT)
    # Le code du serveur que je vous ai fourni accepte les connections sur le endpoint "/messages"
    # Il se connecte à MySQL pour afficher une liste de messages contenus en Base
    # Si vous obtenez ce résultat, alors votre application back-end est bien configurée, et votre base de donnée MySQL aussi
    curl 127.0.0.1:3000/messages
    [{"id":1,"content":"hello"},{"id":2,"content":"world"},{"id":3,"content":"docker"},{"id":4,"content":"compose"}]
    ```
    Si vous n'obtenez pas ce résultat, alors il y a une erreur dans la connexion à la base, il faudra chercher pour la corriger, car elle peut venir de MySQL, de l'utilisateur MySQL, de la configuration de votre application....
- Dans votre navigateur sur votre nom de domaine `nom.holidays.masselab.com:3000` (sur le Port 3000: le port sur lequel l'application tourne), et pour la connexion à la base: `nom.holidays.masselab.com:3000/messages`.

**L'API que nous venons de lancer sur cette nouvelle instance s'éteindra dès lors que vous quitterez votre session SSH, puisque la tâche (`npm run start`, ou `node server.js`) tourne en `foreground`**. Afin de conserver le serveur node en marche à tout moment nous avons plusieurs solutions:
- [tmux](https://doc.ubuntu-fr.org/tmux): Créer une session Shell détachée, nous permettant d'exécuter des commandes qui continueront de tourner lorsqu'on détachera la session.
- [Les services Linux](https://doc.ubuntu-fr.org/creer_un_service_avec_systemd) (Ici, à l'aide de systemd, que nous avons précédemment avec nginx: `systemctl` et `service`). Nous allons pouvoir gérer la configuration de notre processus (et des variables d'environnement) pour faire tourner notre API comme un serveur node, à tout moment.

Ici, nous utiliserons `les services Linux avec systemctl` (une extension à la commande `service` vue précédemment).

Pour déclarer un service, il faut créer un fichier sur notre instance à l'emplacement `/etc/system/system/<nom-du-service>.service`, avec la syntaxe suivante:
```conf
# Configuration principale de notre service
[Service]
# Variables d'environnements à utiliser dans le service
# Ici, spécifiez les variables d'environnement demandées plus haut comme
Environment="NOM_DE_MA_VARIABLE=valeur"
Environment="DB_HOST=127.0.0.1"
Environment="DB_USER=infrauser"
# ...

# Suites de commande à exécuter pour chacun des états du service:
# Ici, cette commande sera exécutée lorsqu'on appellera systemctl start mon-service
ExecStart=cd <dossier-contenant-le-code-serveur> && npm run start
```

Grâce à ce système de service, nous allons pouvoir configurer notre API Back-end. Je vous invite donc à créer un nouveau fichier `/etc/systemd/system/backend.service`, et y renseigner les différentes options nécessaires à la configuration de l'API à savoir:
- Les variables d'environnements avec la clé `Environment` (voir la liste de variables au-dessus).
- La commande `ExecStart` qui sera chargée de démarrer le serveur node.
- La commande `Restart=on-failure` qui sera chargée de relancer le service lorsque celui ci rencontrera une erreur.

Une fois ce nouveau fichier crée, vous devrez indiquer à systemd que la configuration a été modifiée (vous venez d'ajouter un service):
```bash
# Notifie systemd que la configuration a été modifiée, et qu'il faut donc la prendre en compte
sudo systemctl daemon-reload
# Démarrer le nouveau service que vous venez de créer
sudo systemctl start backend
# Vérifier l'état du service
sudo systemctl status backend
● backend.service
   Loaded: loaded (/etc/systemd/system/backend.service; static; vendor preset: enabled)
   Active: active (running) since Sun 2019-10-13 13:51:28 UTC; 11min ago
 Main PID: 2320 (node)
    Tasks: 6 (limit: 1152)
   CGroup: /system.slice/backend.service
           └─2320 /usr/bin/node /home/ubuntu/server/server.js

Oct 13 13:51:28 ip-172-31-12-191 systemd[1]: Started backend.service.
Oct 13 13:51:29 ip-172-31-12-191 node[2320]: Listening on http://localhost:3000
Oct 13 13:51:43 ip-172-31-12-191 node[2320]: GET / 200 4.250 ms - 19
```

**Notez que si vous modifier plusieurs fois le fichier, il faut relancer `systemctl daemon-reload` pour prendre en compte la dernière version de la configuration**.

### Front-End

#### Pré-requis

- Node.js et npm installés
- nginx installé

#### L'exercice


Dans le cadre de notre application Front, nous utilisons React, avec `create-react-app`, [veuillez vous référer à leur documentation](https://create-react-app.dev/docs/adding-custom-environment-variables) concernant la configuration avec variables d'environnement. Pour résumer, on peut utiliser des variables d'environnement dans notre application, il faudra simplement les renseigner (`export MAVARIABLE=VALEUR`) au moment de build le code. Et les références à `process.env.MAVARIABLE` dans le code seront remplacés par la valeur de cette variable (dans cet exemple: VALEUR).

D'abord, vous devrez uploader le code du dossier `client` de ce TP sur votre serveur Linux avec la commande scp:
```bash
scp -r * tp@<ip>:/home/tp/client
```

Il faudra ensuite `builder` l'application, comme vous avez pu le voir en cours avec Bastien Calou avec Parcel (pour SASS, SCSS...).

Vous pourrez utiliser la commande `npm run build` (dans le dossier contenant le code), après avoir exporté la variable `REACT_APP_BACKEND_URL`:
```bash
# On exporte la variable d'environnement qui pointe sur le serveur linux, sur le port 3000 puisque c'est la que tourne l'application Back-End
export REACT_APP_BACKEND_URL=http://<ip-du-serveur>:3000
# Installer les dépendances
npm install
# On build l'application
npm run build
# Un dossier build a été crée dans le dossier de votre code client, avec le code html css js
```

**Maintenant, configurez votre serveur NGINX dans le dossier /etc/nginx/sites-enabled pour pointer sur votre nouveau dossier build (pour servir le code HTML aux navigateurs)**, je ne donne pas de détails sur cette partie, vous avez déjà fait 3 TPs sur la configuration d'NGINX.

Ouvrez ensuite votre site-web (avec votre nom de domaine, fournis dans l'exercice)
![website finale](./assets/app.png).

Vous pouvez vous amuser à modifier le contenu de votre base de données, vous verrez le résultat se mettre à jours sur votre navigateur.
