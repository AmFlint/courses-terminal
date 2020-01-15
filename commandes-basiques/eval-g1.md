# Évaluation

- **Envoyer par mail** à antoine.masselot@hetic.net avec:
  - Objet du mail: `TP Shell G1 NOM Prenom`
  - En pièce jointe votre fichier contenant l'exercice avec la nomenclature suivante: `g1-nom-prenom.txt`

**Un ou plusieurs points seront retirés en cas de non respect des consignes** (lorsqu'on travaille avec un client, si on ne respecte pas le brief, il ne paie pas).

## Exercice 1

1 pts bonus si vous n'utilisez pas la commande `cd` une seule fois dans l'exercice.

- Créer un dossier "nom-prenom"
- Créer un fichier "hello" dans le dossier "nom-prenom"
- Copier le fichier hello dans un nouveau fichier goodbye
- Supprimer le fichier hello
- Copier le dossier "nom-prenom" et son contenu (fichiers) dans un nouveau dossier "mon-dossier-2"
- renommer le dossier "mon-dossier-2" en "mon-dossier-3"
- créer le dossier "mon-sous-dossier" dans "mon-dossier-3"
- Supprimer le dossier "mon-sous-dossier"
- Renommer le fichier "goodbye" en "goodbye-again" (dans le dossier "mon-dossier-3")
- Donner le droit de lecture et d'écriture au groupe (g) sur le fichier goodbye-again
- Supprimer le droit d'exécution pour les 3 types d'utilisateurs au fichier goodbye-again 
- En une seule commande, donner les droits suivants au fichier goodbye-again
  - utilisateurs:
    - lecture
    - écriture
    - exécution
  - groupe:
    - lecture
    - pas écriture
    - exécution
  - others:
    - lecture
    - pas écriture
    - pas exécution
- Afficher les fichiers (détaillés, on doit pouvoir consulter les permissions) présents dans le dossier mon-dossier-3
- Supprimer le fichier goodbye-again
- Supprimer le dossier mon-dossier-3 et son contenu

## Exercice 2

1. Quelle commande utiliser pour installer le packet `ruby` sur Linux ?
2. Je souhaite me connecter avec le protocole SSH à un serveur distant avec l'IP 163.172.4.123 avec l'utilisateur `masselot`, quelle commande (commande complète) utiliser ?
3. Je souhaite uploader tous les fichiers du dossier dans lequel je me trouve (dans mon terminal), sur le même serveur avec le même utilisateur que la question au-dessus. Je souhaite uploader ces fichiers dans un dossier `/home/masselot/exercice`, quelle commande (complète) dois-je exécuter ?
4. Quel paramètre dans la configuration du serveur Web NGINX permet de spécifier dans quel dossier se trouve le code de mon site, à "servir" ?
5. J'utilise la commande `rm /etc/nginx/sites-enabled/default`, je reçois l'erreur `Permission Denied`. Quelle commande pourrai-je utiliser pour forcer la suppression du fichier `/etc/nginx/sites-enabled/default` ?
6. Comment s'appelle le processus qui nous permet de récupérer l'adresse IP des serveurs derrière le nom de domaine `google.com` ?
7. Quelle commande permet de redémarrer un service sur Linux (par exemple `nginx`) ?

