# Évaluation

- **Format de rendu**: un fichier au format `.sh` dans lequel vous écrirez les différentes instructions Shell pour résoudre l'exercice.
- **Envoyer par mail** à amasselot@hetic.net avec:
  - Objet du mail: `TP Shell G2s NOM Prenom`
  - En pièce jointe votre fichier contenant l'exercice avec la nomenclature suivante: `g2-nom-prenom.sh` et `g2-nom-prenom.txt`
- Vous avez accès à Internet pendant l'épreuve, vous pouvez chercher la documentation en ligne mais pas communiquez entre vous.

**Un ou plusieurs points seront retirés en cas de non respect des consignes** (lorsqu'on travaille avec un client, si on ne respecte pas le brief, il ne paie pas).

## Exercice 1

- Créer un dossier "nom-prenom"
- Créer un fichier "goodbye" dans le dossier "nom-prenom"
- Copier le dossier "nom-prenom" dans un nouveau dossier "nouveau-dossier"
- Déplacer le fichier "goodbye" (présent dans le dossier "nom-prenom") dans le dossier "nouveau-dossier" avec comme nouveau nom "goodbye-again"
- Supprimer le dossier "nom-prenom"
- Supprimer le fichier "goodbye" dans le dossier "nouveau-dossier"
- Supprimer le fichier "goodbye-again" dans le dossier "nouveau-dossier"
- Renommer le dossier "nouveau-dossier" en "mon-dossier"
- Créer un fichier "mon-fichier" dans le dossier "mon-dossier"
- Donner le droit de lecture et d'exécution à l'utilisateur à qui appartient le fichier "mon-fichier"
- Supprimer le droit d'écriture au groupe d'utilisateur à qui appartient le fichier "mon-fichier"
- En une seule commande, donner les droits suivants au fichier mon-fichier
  - utilisateurs:
    - lecture
    - écriture
    - exécution
  - groupe:
    - pas lecture
    - écriture
    - exécution
  - others:
    - pas lecture
    - pas écriture
    - exécution
- Afficher les fichiers (affichage détaillé, on doit pouvoir consulter les permissions) dans le dossier "mon-dossier"
- Afficher le contenu du fichier "mon-fichier" dans le terminal
- Supprimer le dossier "mon-dossier"

## Exercice 2

1. Quel paramètre dans la configuration du serveur Web NGINX permet de spécifier quel nom de domaine utiliser pour appliquer la configuration ?
2. Quelle commande permet de démarrer un service sur Linux (par exemple `nginx`) ?
3. J'utilise la commande `rm /etc/nginx/sites-enabled/default` (j'ai la permission `r--` et le fichier m'appartient), je reçois l'erreur `Permission Denied`. Quelle commande (complète, avec les arguments) pourrai-je utiliser pour modifier les permissions du fichier pour pouvoir le supprimer ?
4. Je souhaite me connecter avec le protocole SSH à un serveur distant avec l'IP 163.121.4.123 avec l'utilisateur `tp`, quelle commande (commande complète) utiliser ?
5. Qu'est ce que la résolution DNS (en 1 phrase) ?
6. Je souhaite uploader le fichier `index.js` du dossier dans lequel je me trouve (dans mon terminal), sur le même serveur avec le même utilisateur que la question au-dessus. Je souhaite uploader ces fichiers dans un dossier `/home/masselot/exercice.js`, quelle commande (complète) dois-je exécuter ?
7. Quelle commande utiliser pour installer le packet `mysql-client` sur Linux ?
