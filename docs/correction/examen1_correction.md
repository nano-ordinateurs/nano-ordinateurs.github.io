# Correction de l'examen 1

Le calcul des notes est fait de la façon suivante :
```
0.5 * (Note de la section Raspberry Pi) + 0.5 * (Note de la section Sense Hat) = Note de votre examen
```

Le pointage de l'examen a quelque peu été modifié afin de compenser pour la difficulté de la question 4 de la partie Sense Hat (**Personne n'a été désavantagé par ce pointage**).

---
# Correction Raspberry Pi

### Question 1 (/3)

Si vous preniez des captures d'écran du Raspberry Pi Imager lors de la configuration, vous aviez automatiquement tous vos points.

### Question 2

Plusieurs méthodes étaient possibles ; si la commande `avahi-browse` était utilisée, vous aviez vos points.

```sh
sudo avahi-browse --all --resolve
```

### Question 3

Vous aviez tous vos points peu importe la manière dont vous vous êtes connecté (grâce à l'adresse IP ou grâce au `hostname`).

```sh
ssh user@hostname.local
```

### Question 4

Ce qui était évalué ici, c'était votre capacité à transférer et exécuter un programme sur un environnement qui n'était pas le vôtre.

Si vous aviez fait un `hello world` sur une machine.

### Question 5

Il fallait simplement être en mesure de modifier le programme à l'intérieur de votre Raspberry Pi. Vous pouviez utiliser votre éditeur de texte préféré (vim, nano, emacs, etc.).

### Question 6

Vous deviez télécharger le nouveau fichier depuis votre Raspberry Pi vers votre ordinateur avec la commande `scp` :

```sh
scp user@rpi:<chemin du fichier> .
```

### Question 7

L'interface recherchée pouvait être affichée avec la commande `nmtui`. D'autres outils pouvaient être utilisés, comme `nmcli` par exemple, mais dans ce cas il fallait utiliser toutes les commandes permettant de se déconnecter, de regarder les réseaux disponibles et finalement de se connecter au Wi-Fi.

### Question 8

```sh
ip route
```

Plus de la moitié des points étaient accordés si vous aviez utilisé la commande `ip`, même sans être capable de trouver exactement l'IP du routeur.

### Question 9

```sh
sudo adduser nom
```

Pour montrer la présence d'un utilisateur, il y avait beaucoup de réponses possibles. Une capture d'écran de la liste des utilisateurs ou bien une capture de l'intérieur du répertoire `home` suffisait.

---
# Correction Sense hat


### 1

```py
from sense_emu import SenseHat
import time
sense = SenseHat()

rouge = (255, 0, 0)
blanc = (255, 255, 255)

def clignotement(couleur, nb_fois = 3, duree = 0.3):
    for i in range(nb_fois):
        sense.clear(couleur)
        time.sleep(duree)
        sense.clear()
        time.sleep(duree)

while True:

    humidite = sense.get_humidity()
    temperature = sense.get_temperature()

    if humidite > 75 and temperature > 0:
        clignotement(blanc)

    elif humidite > 75:
        sense.show_message("Danger - Humidite")


    elif temperature > 0:
        clignotement(rouge)

    time.sleep(0.2)
```

### 2

```py
from sense_emu import SenseHat

sense = SenseHat()

filename = "input.rec"

with open(filename, "w") as f:
    while True:
        for event in sense.stick.get_events():
            if event.action == "pressed":
                f.write(event.direction + ",\n")
```

### 3


```py
from sense_emu import SenseHat
import time

sense = SenseHat()

with open("nouvelle.txt", encoding="utf-8") as f:
    lignes = f.read().splitlines()

while True:
    for ligne in lignes:
        sense.show_message(ligne)
        time.sleep(3)
    break
```

### 4

Il y avait 4 point allouer a ce numéro il y a en eu 2 finalement. 

4 point : le jeu fonctionne parfaitement.
3 point : le jeu fonctionne mais ne réponds pas à tout les spécifications.
2 point : les grands méchanisme du jeu fonctionne (choix aléatoire et boucle cohérente).
1 point : un méchanisme important est mis en valeur.

```py
from sense_emu import SenseHat
from random import randrange
import time

sense = SenseHat()

rouge = (255, 0, 0)
bleu = (0, 0, 255)
vert = (0, 255, 0)
jaune = (255, 255, 0)

couleur = [rouge, bleu, vert, jaune]


def ajouter_couleur(liste_couleur):
    liste_couleur.append(couleur[randrange(len(couleur))])


def clignotement(couleur, nb_fois=1, duree=0.3):
    for i in range(nb_fois):
        sense.clear(couleur)
        time.sleep(duree)
        sense.clear()
        time.sleep(duree)


def match_couleur(direction):
    match direction:
        case "up":
            return rouge
        case "right":
            return bleu
        case "down":
            return jaune
        case "left":
            return vert
        case _:
            return None


def afficher_sequence(sequence):
    for i in range(len(sequence)):
        sense.clear(sequence[i])
        time.sleep(1)
        sense.clear()
        time.sleep(0.5)


def attendre_debut():
    while True:
        event = sense.stick.wait_for_event()
        if event.action == "pressed" and event.direction == "middle":
            return


def lire_joueur():
    while True:
        event = sense.stick.wait_for_event()
        if event.action != "pressed":
            continue
        if event.direction == "middle":
            continue
        c = match_couleur(event.direction)
        if c is not None:
            return c


def game_loop():
    attendre_debut()
    
    sequence = []
    ajouter_couleur(sequence)
    perdu = False
    
    while not perdu:
        afficher_sequence(sequence)
        
        for i in range(len(sequence)):
            jouee = lire_joueur()
            if jouee != sequence[i]:
                perdu = True
                break
        
        if not perdu:
            sense.show_message("next level")
            ajouter_couleur(sequence)
    
    sense.show_message("game over")
    sense.clear()


game_loop()

```