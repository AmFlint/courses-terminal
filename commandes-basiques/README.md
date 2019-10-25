# Commandes basiques

**Les exercices liés aux commandes basiques sont indiqués plus bas sur cette même page**.

Ici, les étudiants pourront trouver une liste d'exercices et de supports/tutoriaux pour les commandes de bases `Shell`. Ces commandes de bases sont orientées sur la `gestion du système de fichier`, c'est à dire faire ce que fait le Finder/Explorateur depuis notre terminal.

Les commandes à connaître:
- `pwd` (print working directory): Afficher le dossier courant (le dossier duquel nos commandes seront exécutées).
- `cd` (change directory): Changer le dossier courant (working directory)
- `ls`: Lister les fichiers et dossiers présents dans un dossier spécifique
- `mkdir`: Créer un nouveau dossier (make directory) 
  ```bash
  mkdir nom-du-nouveau-dossier
  ```
- `touch`: Créer un fichier
  ```bash
  touch nom-du-fichier
  ```
- `mv` (move): Déplacer un fichier/dossier (peut donc servir à déplacer un fichier d'un emplacement A à un emplacement B, à renommer un fichier ou les deux en même temps)
  ```bash
  mv fichier-a-deplacer nouvel-emplacement
  ```
- `cp` (Copy): Copier le contenu d'un fichier vers un nouveau fichier:
  ```bash
  cp fichier-a-copier fichier-a-coller
  ```
  Pour copier un dossier et son contenu, on utilise l'option `-R`:
  ```bash
  cp -R dossier-a-copier dossier-a-coller
  ```
- `rm` (Remove): Supprimer un fichier (**Attention, les fichiers supprimés avec rm le sont définitivement, pas de retour en arrière possible**):
  ```bash
  rm fichier-a-supprimer
  ```
  Pour supprimer un dossier et son contenu, on utilise l'option `-r`:
  ```bash
  rm -r dossier-a-supprimer
  ``` 

## Les Exercices

[Une correction est disponible pour chacun des exercices présenté ci-dessous à cet endroit](./CORRECTIONS.md).

### Exercice 1

1. créer dossier mondossier
2. créer fichier hello.txt dans mondossier
3. écrire 'Hello' dans hello.txt
4. afficher le contenu du fichier hello.txt dans le terminal
5. renommer le fichier hello.txt en goodbye.txt
6. dupliquer le dossier mondossier (et son contenu) dans un dossier nouveaudossier
7. afficher le contenu du dossier nouveaudossier
8. supprimer le dossier mondossier
9. supprimer le fichier goodbye.txt dans le dossier nouveaudossier
10. supprimer le dossier nouveaudossier

### Exercice 2

1. Créer un fichier "monfichier.txt"
2. Écrire le texte "Hello" dans le fichier "monfichier.txt" (ouvrez votre éditeur de texte si nécessaire)
3. Dupliquer le fichier "monfichier.txt" vers un nouveau fichier "monnouveaufichier.txt"
4. Créer un dossier mondossier
5. Déplacer le fichier "monnouveaufichier.txt" dans le dossier "mondossier"
6. Renommer le dossier "mondossier" en "monnouveaudossier"
7. Afficher le contenu (ls) du dossier "monnouveaudossier"
8. Supprimer le fichier "monfichier.txt"
9. Copier le dossier "monnouveaudossier" dans un nouveau dossier "dossiercp"
10. Supprimer le dossier "monnouveaudossier"
11. Afficher le contenu du fichier "monnouveaufichier.txt", présent dans le dossier "dossiercp"
12. Supprimer le dossier "dossiercp"

### Exercice 3

1. Créer un dossier "mondossier"
2. Créer un dossier "monnouveaudossier"
3. Créer un fichier "monfichier" dans "mondossier", et un autre fichier "monautrefichier" dans "monnouveaudossier"
4. Déplacer "monfichier" présent dans mondossier vers le dossier "monnouveaudossier" et renommer le fichier "monnouveaufichier"
5. Copier "monnouveaufichier" présent dans le dossier "monnouveaudossier" vers le dossier "mondossier" et appeler le fichier "monfichier"
6. Déplacer le dossier "monnouveaudossier" dans le dossier "mondossier" (sans le renommer)
7. Créer un fichier "fichier" dans le dossier "monnouveaudossier" (lui même dans le dossier "mondossier")
8. Supprimer le dossier "mondossier"
