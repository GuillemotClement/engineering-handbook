# WSL 

Techno Microsoft qui permet de lancer des distributions Linux directement sous Windows.
- **WSL1** : fonctionne comme une compatibilité au niveau des appels systèmes entre Windows et Linus. Elle émule le comportement de Linux via le kernel Window
- **WSL2**: utilise un véritable kernel Linux via une VM. Plus rapide et meilleur compatibilité avec le vrai Linux.

Cette techno permet d'obtenir des outils Linux en restant dans l'écosystème Window. 

## Installation 

```powershell 
# vérifier la version de window
winver

# installation de wsl 
wsl --install  
```

Lorsque la commande d'installation est lancer, cela vient installer automatiquement le noyau Linux et configure WLS2 par défaut.

## Choix de distribution 

```powershell 
# lister les distributions Linux disponible
wsl --list --online

# liste les distributions installées 
wsl --list --verbose

# installer une distribution 
wsl --install -d <name_distri>

# stopper les distribution 
wsl --shutdown 

# supprimer une distribution 
wsl --unregister <name_distrib>

# ouvrir WSL dans un répertoire spécifique 
wsl ~/
```

## Configuration 

Pour utiliser WSL2 par défaut 

```powershell 
# passer wsl2 par défaut 
wsl --set-default-version 2

# changer la version 
wsl --set-version <name_distrib> 2 

```

## Intégration WSL et Windows 

WSL s'intègre avec Windows, ce qui permet d'utiliser les fonctionnalités des deux systèmes.

### Partage de fichier 

WSL est monter dans le système de fichier Windows dans le répertoire `\mnt`

```powershell
cd /mnt/c/Users/<username>
```

On peut venir travailler avec des fichiers Windows directement depuis Linux.
De la même manière, on peut utiliser des fichiers WSL dans Windows via un chemin `\\wsl$\<name_distribution>`

### Exécuter des commandes Linux 

```powershell
wsl ls
```






