# Corrections des exercices

Ici, vous trouverez la correction des différents exercices/TPs proposés dans ce repository.

## Exercice 1 - Correction

```bash
# 1. Créer dossier mondossier
mkdir mondossier
# 2. Créer fichier hello.txt dans mondossier
# D'abord, changer de working directory en entrant dans mondossier avec la commande cd
cd mondossier
touch hello.txt
# 3. écrire 'Hello' dans hello.txt
# J'affiche 'Hello' dans le terminal avec la commande echo, puis je capture la sortie de cette commande pour l'écrire dans le fichier hello.txt
echo Hello > hello.txt
# 4. Afficher le contenu du fichier hello.txt dans le terminal
cat hello.txt
# 5. renommer le fichier hello.txt en goodbye.txt
mv hello.txt goodbye.txt
# 6. dupliquer le dossier mondossier (et son contenu) dans un dossier nouveaudossier
# D'abord je dois changer mon working directory avec la commande cd, pour revenir en arrière de 1 dossier dans mon système, j'utilise '..'
# cp -R pour copier un dossier, mondossier et le fichier à copier, nouveaudossier est l'emplacement où je veux coller le dossier.
cd ..
cp -R mondossier nouveaudossier
# 7. Afficher le contenu du dossier nouveaudossier
ls nouveaudossier
# Ou alors, je peux changer mon working directory avec cd, puis utiliser ls sans argument, puis revenir en arrière de un avec la commande cd ..
cd nouveaudossier
ls
cd ..
# 8. supprimer le dossier mondossier
rm -r mondossier
# 9. supprimer le fichier goodbye.txt dans le dossier nouveaudossier
# Première méthode: je change le working directory avec cd pour entrer dans nouveaudossier, puis je supprime le fichier avec rm
cd nouveaudossier
rm goodbye.txt
cd ..
# Deuxième méthode: J'indique le chemin relatif de mon current working directory jusqu'au fichier à supprimer
rm nouveaudossier/goodbye.txt
# 10. supprimer le dossier nouveaudossier
rm -r nouveaudossier

```

## Exercice 2 - Correction

```bash
# 1. Créer un fichier "monfichier.txt"
touch monfichier.txt
# 2. Écrire le texte "Hello" dans le fichier "monfichier.txt"
echo Hello > monfichier.txt
# 3. Dupliquer le fichier "monfichier.txt" vers un nouveau fichier "monnouveaufichier.txt"
cp monfichier.txt monnouveaufichier.txt
# 4. Créer un dossier mondossier
mkdir mondossier
# 5. Déplacer le fichier "monnouveaufichier.txt" dans le dossier mondossier
mv monnouveaufichier.txt mondossier
# 6. Renommer le dossier "mondossier" en "monnouveaudossier"
mv mondossier monnouveaudossier
# 7. Afficher le contenu du dossier "monnouveaudossier"
# Première méthode, je change de working directory avec la commande cd, puis je liste les fichiers
cd monnouveaudossier
ls
cd ..
# Deuxième méthode: Je liste le contenu du dossier monnouveaudossier depuis mon current working directory avec la technique relative
ls monnouveaudossier
# 8. Supprimer le fichier "monfichier.txt"
rm monfichier.txt
# 9. Copier le dossier monnouveaudossier dans un nouveau dossier dossiercp
cp -R monnouveaudossier dossiercp
# 10. Supprimer le dossier monnouveaudossier
rm -r monnouveaudossier
# 11. Afficher le contenu du fichier monnouveaufichier.txt, présent dans le dossier dossiercp
# Première méthode: Je change mon working directory avec la commande cd pour entrer dans dossiercp puis j'affiche le contenu du fichier avec la commande cat
cd dossiercp
cat monnouveaufichier.txt
cd ..
# Deuxième méthode: J'affiche le contenu du fichier monnouveaufichier.txt depuis mon current working directory avec la technique relative
cat dossiercp/monnouveaufichier.txt
# 12. Supprimer le dossier dossiercp
rm -r dossiercp
```

## Exercice 3 - Correction

```bash
# 1. Créer un dossier mondossier
mkdir mondossier
# 2. Créer un fichier monnouveaudossier
mkdir monnouveaudossier
# 3. Créer un fichier "monfichier" dans "mondossier", et un autre fichier "monautrefichier" dans "monnouveaudossier"
# Ici, on peut utiliser le chemin relatif depuis notre working directory pour créer des fichiers dans un dossier, on peut également créer plusieurs fichiers dans la même commande, notez que chaque argument (nouveau fichier) repart du current working directory, nous devons donc respécifier le chemin vers le dossier
touch mondossier/monfichier monnouveaudossier/monautrefichier
# 4. Déplacer "monfichier" présent dans "mondossier" vers le dossier "monnouveaudossier" et renommer le fichier "monnouveaufichier"
mv mondossier/monfichier monnouveaudossier/monnouveaufichier
# 5. Copier "monnouveaufichier" présent dans le dossier "monnouveaudossier" vers le dossier "mondossier" et appeler le fichier "monfichier"
cp monnouveaudossier/monnouveaufichier mondossier/monfichier
# 6. Déplacer le dossier "monnouveaudossier" dans le dossier "mondossier" (sans le renommer)
# Ici, vu que mondossier existe déjà, le programme mv va comprendre qu'il faut déplacer monnouveaudossier dans le dossier mondossier et non pas le renommer
mv monnouveaudossier mondossier
# 7. Créer un fichier "fichier" dans le dossier "monnouveaudossier" (lui même dans le dossier "mondossier")
touch mondossier/monnouveaudossier/fichier
# 8. Supprimer le dossier mondossier
rm -r mondossier
```
