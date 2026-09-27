# Usage du Raspberry Pi

Le Raspberry Pi est avant tout un ordinateur ; on peut l'utiliser comme tel (avec un clavier et un écran). Or, cet usage n'est pas très pratique. Dans de nombreux cas, on cherche à faire en sorte que le Raspberry Pi fasse tourner un logiciel en continu sans qu'on ait activement à le surveiller. Parfois, l'accès physique au Raspberry Pi est impossible (par exemple sur un drone en vol ou sur une station météo lointaine). On préconise donc l'accès à distance au Raspberry Pi, qui est bien plus pratique et facile. Cependant, pour comprendre comment accéder à un Raspberry Pi et l'utiliser, une courte révision s'impose !



# Linux

Tous les systèmes d'exploitation du Raspberry Pi sont basés sur UNIX ; il est donc essentiel de comprendre certaines commandes de base afin de réussir à utiliser le Raspberry Pi de manière efficace. La commande la plus importante de Linux est `man` : lorsqu’elle est suivie d’une commande, elle permet de consulter sa documentation. Le manuel est en règle générale toujours assez fourni pour vous permettre d’être au moins capable de faire un usage simple de la commande.
<figure markdown> ![rtfm](../../assets/images/rtfm.jpg) <figcaption>[RTFM](https://fr.wikipedia.org/wiki/RTFM_(expression))</figcaption> </figure>

Si vous avez de la difficulté à utiliser Linux, je vous recommande les jeux éducatifs suivants : [Terminus](https://luffah.xyz/bidules/Terminus/) ou bien [Bashcrawl](https://bamr87.github.io/bashcrawl/#/story), qui sont accessibles à partir de votre navigateur web.

## Se connecter au wifi par CLI

On peut se connecter au wifi de plusieurs manières. Cependant la suite  Network Manager est le moyen le plus pratique de le faire.


Avec nmcli,  on a : 
```bash
sudo nmcli connection up "NomDeLaConnexion"
```

Cependant une manière plus simple de faire est d'utiliser nmtui qui offre une interface plus commode.
```bash
nmtui
```





## Déplacement dans une arborescence de fichier


Pour voir les fichiers présents dans le répertoire courant, on peut utiliser la commande `ls`. Pour se déplacer dans un répertoire particulier, il y a la commande `cd` suivie du nom du répertoire. Pour savoir où l’on est, on peut utiliser la commande `pwd`.

Dans Linux, il y a deux entrées spéciales dans tous les répertoires.
Le fichier . pointe vers le répertoire courant.
Le fichier .. pointe vers le répertoire parent.

```sh
pwd              # affiche le chemin du répertoire courant
ls               # liste les fichiers du répertoire courant
ls -la           # liste tous les fichiers, y compris les fichiers cachés, avec détails
cd Documents     # entre dans le dossier Documents (chemin relatif)
cd /home/pi      # va dans /home/pi (chemin absolu)
cd ..            # remonte d’un niveau, vers le répertoire parent
cd ../..         # remonte de deux niveaux
cd ~             # va dans le répertoire personnel de l’utilisateur
cd               # équivaut à cd ~
cd -             # retourne au répertoire précédent
```





# Réseautique


## DHCP

Le protocole DHCP permet de fournir automatiquement une adresse IP localement unique aux machines qui se connectent au réseau. Il fournit :

- le masque de sous-réseau ;
- la passerelle par défaut (*default gateway*) qui est l'adresse du routeur ;
- les serveurs DNS ;
- la durée de validité de cette attribution.



**Commandes utiles :**

```bash
ip a # affiche les reseaux ou on est connecter 
ip route # affiche l'ip du routeur
```

## DNS
Le protocole DNS associe des noms de domaine à des adresses IP. Il maintient des tables de correspondance pour éviter de retenir les adresses IP. Les serveurs DNS se réfèrent à d'autres serveurs DNS de manière récursive lorsqu'ils ne trouvent pas l'adresse IP, jusqu'à atteindre l'organisme qui alloue les noms de domaine.

Exemple :

```text
google.com -> 142.250.72.14
```

**Commandes utiles :**

```bash
nmcli # permet aussi de voir le client
cat /etc/resolv.conf # votre table locale que vous pouver configurer comme vous le voulez
nslookup google.com # Va demander au serveur DNS quel est l'adresse ip de google
ping google.com # S'assure qu
```


## mDNS

La mise en place d'un serveur DNS sur un réseau local peut être laborieuse. Il est possible de faire de la résolution de noms sans avoir à maintenir une table de correspondance, en utilisant le multicast (qui avertira toutes les machines sur le réseau de son nom). Cela nous permet de désigner un Raspberry Pi par son nom d'hôte suivi de `.local`. Par exemple :

```bash
ping raspberrypi.local
ssh user@raspberrypi.local
```

Le service qui offre cette fonctionnalité est **Avahi**. Le paquet `avahi-utils` est essentiel pour utiliser les outils en ligne de commande. Pour l'installer :

```bash
sudo apt update
sudo apt install avahi-utils
```

Si l'on veut que le nom d'hôte de notre machine soit accessible via mDNS, il faut s'assurer que le nom d'hôte est correctement défini et que le service Avahi est actif. Par exemple, pour définir le nom d'hôte :

```bash
sudo hostnamectl set-hostname mon-raspberry
```

Ensuite, on redémarre le service Avahi pour prendre en compte le changement :

```bash
sudo systemctl restart avahi-daemon
```

On peut vérifier que le service est actif :

```bash
sudo systemctl status avahi-daemon
```

Avahi permet aussi de découvrir les appareils nommés sur notre réseau. Pour lister tous les services mDNS disponibles :

```bash
avahi-browse --all --resolve
```


## SSH

SSH (*Secure Shell*) permet d’ouvrir un terminal sur une machine distante de façon sécurisée. On peut s'authentifier par mot de passe ou par clé cryptographique.

**Activer SSH sur le Raspberry Pi :**

```bash
sudo systemctl enable --now ssh
sudo systemctl status ssh
```

Ou via :

```bash
sudo raspi-config
```

Dans Interface Options, SSH, puis Enable.

**Se connecter :**

```bash
ssh user@adresse_ip
ssh user@raspberrypi.local
```

Exemple :

```bash
ssh pi@192.168.1.50
```

**Copier un fichier avec SCP :**

```bash
scp fichier.txt user@192.168.1.50:/home/user/
```

**Télécharger un fichier dans le répertoire courant :**

```bash
scp user@192.168.1.50:/home/user/fichier.txt .
```






## Annexe

### Commandes de base

| Tâche | Commande |
|---|---|
| Afficher le répertoire courant | `pwd` |
| Afficher les fichiers dans le répertoire | `ls` |
| Se déplacer dans le système de fichiers | `cd` |
| Créer un fichier | `touch` |
| Déplacer un fichier | `mv` |
| Supprimer un fichier | `rm` |
| Copier un fichier | `cp` |
| Afficher le contenu d'un fichier | `cat` |
| Voir une arborescence de fichiers | `tree` |
| Trouver un fichier en particulier | `find` |
| Changer les permissions d'un fichier | `chmod` |
| Faire exécuter une commande en tant que superutilisateur | `sudo` |
| Mettre à jour | `sudo apt update && sudo apt upgrade` |
| Voir les processus | `ps` |
| Tuer un processus | `kill` |

### Commandes réseau

| Tâche | Commande |
|---|---|
| Configurer le Wi-Fi | `nmtui` |
| Redémarrer le réseau | `sudo systemctl restart NetworkManager` |
| Activer SSH | `sudo systemctl enable --now ssh` |
| Connexion SSH | `ssh user@ip` |

### Commandes d'administration

| Tâche | Commande |
|---|---|
| Lister les utilisateurs | `cat /etc/passwd` |
| Ajouter un utilisateur | `sudo adduser nom` |
| Ajouter au groupe sudo | `sudo usermod -aG sudo nom` |
| Mettre à jour | `sudo apt update && sudo apt upgrade` |
| Installer un paquet | `sudo apt install nom` |
