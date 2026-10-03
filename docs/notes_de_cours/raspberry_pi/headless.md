# Connexion headless

Dans beaucoup de contextes, il est souvent très inefficace de dépendre d'un écran lorsqu'on travaille avec un Raspberry Pi. Or, pour accéder au SSH, il faut préalablement avoir une machine qui comporte un service SSH ouvert et une connexion à un réseau. Il faut donc trouver un moyen de configurer préalablement le Raspberry Pi afin qu'il se connecte au réseau et permette aux autres de se connecter à lui par SSH. Il existe deux façons de faire cela :

- avec le Raspberry Pi Imager (façon simple) ;
- en jouant avec les fichiers.

## Configuration

Pour ce faire, on utilise le `rpi-imager`. Cette fois, nous allons personnaliser (avec l'onglet Customisation) notre système d'exploitation avec un `hostname`, un `username` ainsi qu'un mot de passe dont nous allons devoir nous rappeler.

Pour rappel, le caractère `@` sépare le `username` du `hostname` :

```text
     _________________        ______________________
    | nom de l'usager |      |  nom de l'ordinateur |
     _________________        _______________________
            |                   |
            |                   |
            v                   v
       jean_pierre@ordinateur_maison
```

*Pour les étapes suivantes, veuillez prendre en note vos informations de configuration afin de ne pas les oublier.*

1. Il faut d'abord choisir un nom d'hôte. Si le nom d'hôte n'est pas choisi, le nom d'hôte par défaut sera choisi (`raspberrypi`).
2. Ensuite, on sélectionne Canada lorsqu'il vient le temps de choisir la localisation.
3. On peut maintenant choisir un utilisateur et lui donner un mot de passe.
4. On configure la connexion Wi-Fi (nom du réseau et mot de passe).
5. On active SSH :
    - avec un mot de passe, dans ce cas il faudra fournir un mot de passe à la connexion ;
    - avec une clé publique disponible sur votre ordinateur.
6. On charge l'OS sur notre carte microSD.

Pour comprendre le fonctionnement du logiciel `rpi-imager`, je vous conseille de jeter un coup d'oeil au fichier `first_run.sh` *avant* le premier lancement.

## Connexion

La première étape est de vérifier si le Raspberry Pi est bien connecté au réseau local. Pour ce faire, on utilise la commande suivante :

```sh
sudo avahi-browse --all
```

Vous devriez voir le nom d'hôte de votre Raspberry Pi. Si ce n'est pas le cas, votre Raspberry Pi n'est sans doute pas connecté au Wi-Fi.

!!!  Si avahi-browse ne vous donne aucun résultat
    Il est possible que `avahi-browse` ne vous donne aucun résultat. Il faut savoir que, par défaut, Windows utilise un mode `NAT` qui fait qu'à partir de WSL vous n'avez accès qu'à un sous-réseau virtuel privé. Vous ne pouvez donc pas voir les communications multicast, qui sont la base du protocole mDNS.

    Pour résoudre le problème, il faut indiquer de manière explicite à Windows que WSL doit être dans le même réseau. Pour ce faire, il faut faire la modification suivante dans `C:\Users\<VotreNom>\.wslconfig` :

```ini
[wsl2]
networkingMode=mirrored
dnsTunneling=false
```

Redémarrez ensuite WSL avec la commande suivante dans PowerShell :

```powershell
wsl --shutdown
```
Puis relancez WSL.



Vous pouvez par la suite vous connecter à votre Raspberry Pi avec SSH.