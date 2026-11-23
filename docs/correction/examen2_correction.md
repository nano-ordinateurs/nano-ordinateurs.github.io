## Question 1
```py
from machine import Pin, ADC
import time

pot = ADC(Pin(34))

pot.atten(ADC.ATTN_11DB)
pot.width(ADC.WIDTH_12BIT)

del1 = Pin(23, Pin.OUT)

btn1 = Pin(32, Pin.IN, Pin.PULL_DOWN)

slp = 1

btn1_etat_nouveau = 0
btn1_etat_ancien = 0


def clignotement(val_pot, slp=1):
    modificateur_sleep = (val_pot + 1000) / (4095 / 2)
    new_slp = slp * modificateur_sleep
    print("valeur du potentiometre : " + str(val_pot))
    print("valeur du nouveau sleep : " + str(new_slp))
    return new_slp


while True:
    btn1_etat_nouveau = btn1.value()

    new_slp = clignotement(pot.read())

    if btn1_etat_ancien == 0 and btn1_etat_nouveau == 1:
        slp = new_slp

    btn1_etat_ancien = btn1_etat_nouveau

    del1.on()
    time.sleep(slp)          # ← float accepté, plus de int()
    del1.off()
    time.sleep(slp)
```


## Question 2

```py
from machine import Pin
import time

btn_inc = Pin(32, Pin.IN, Pin.PULL_DOWN)
btn_dec = Pin(33, Pin.IN, Pin.PULL_DOWN)

dels = [Pin(21, Pin.OUT), Pin(22, Pin.OUT), Pin(23, Pin.OUT)]

compteur = 0

# Etats des boutons
btn_inc_ancien = 0
btn_dec_ancien = 0


# La meilleure solution c'est d'utiliser les operateurs binaires.
# Cependant faire des IF-ELIF est une solution equivalente.
def convertir(num):
    for i in range(3):
        bit = (num >> i) & 1
        dels[i].value(bit)


while True:
    btn_inc_nouveau = btn_inc.value()
    btn_dec_nouveau = btn_dec.value()

    # Detection d'un nouvel appui sur le bouton d'incrementation
    if btn_inc_nouveau == 1 and btn_inc_ancien == 0:
        compteur += 1
        print("Compteur :", compteur)

    # Detection d'un nouvel appui sur le bouton de decrementation
    if btn_dec_nouveau == 1 and btn_dec_ancien == 0:
        compteur -= 1
        print("Compteur :", compteur)

    btn_inc_ancien = btn_inc_nouveau
    btn_dec_ancien = btn_dec_nouveau

    convertir(compteur)

    # Rebouclage : apres 7 on revient a 0, et apres 0 on revient a 7
    # En Python, -1 % 8 == 7, donc le modulo gere automatiquement les deux sens.
    compteur = compteur % 8

    time.sleep_ms(20)
```


## Question 3


```py
from machine import Pin
import time

btn_droite = Pin(32, Pin.IN, Pin.PULL_DOWN)   # deplace le curseur vers la droite (+1)
btn_gauche = Pin(33, Pin.IN, Pin.PULL_DOWN)   # deplace le curseur vers la gauche (-1)

dels = [Pin(21, Pin.OUT), Pin(22, Pin.OUT), Pin(23, Pin.OUT)]

# Index de la DEL allumee : 0 = GPIO 21, 1 = GPIO 22, 2 = GPIO 23
position = 0

btn_droite_ancien = 0
btn_gauche_ancien = 0


def afficher(position):
    for i in range(3):
        dels[i].value(1 if i == position else 0)


while True:
    btn_droite_nouveau = btn_droite.value()
    btn_gauche_nouveau = btn_gauche.value()

    # Detection d'un nouvel appui sur le bouton de droite
    if btn_droite_nouveau == 1 and btn_droite_ancien == 0:
        position = (position + 1) % 3   # rebouclage : apres la DEL de droite, on revient a gauche
        print("Position :", position)

    # Detection d'un nouvel appui sur le bouton de gauche
    if btn_gauche_nouveau == 1 and btn_gauche_ancien == 0:
        position = (position - 1) % 3   # rebouclage : apres la DEL de gauche, on revient a droite
        print("Position :", position)

    btn_droite_ancien = btn_droite_nouveau
    btn_gauche_ancien = btn_gauche_nouveau

    afficher(position)
    time.sleep_ms(20)

```

## Question 4

```py
from machine import Pin, ADC
import time
import random

pot = ADC(Pin(34))
pot.atten(ADC.ATTN_11DB)
pot.width(ADC.WIDTH_12BIT)

btn = Pin(32, Pin.IN, Pin.PULL_DOWN)

MARGE = 100

# Nombre aleatoire entre 0 et 4095, choisi une seule fois au demarrage
nombre_secret = random.randint(0, 4095)

btn_ancien = 0


while True:
    btn_nouveau = btn.value()

    if btn_nouveau == 1 and btn_ancien == 0:
        choix = pot.read()
        print("Choix :", choix, "| Secret :", nombre_secret)  

        if abs(choix - nombre_secret) <= MARGE:
            print("Victoire ! Le nombre etait", nombre_secret)
            break                                   

        elif choix > nombre_secret:
            print("Nombre trop grand")

        else:
            print("Nombre trop petit")

    btn_ancien = btn_nouveau
    time.sleep_ms(20)

```

## Question 5

```py

from machine import Pin, ADC
import time

pot = ADC(Pin(34))
pot.atten(ADC.ATTN_11DB)
pot.width(ADC.WIDTH_12BIT)

code = [1234, 4021, 2560]

TOLERANCE_STABLE = 30    # fluctuation tolérée pour considérer le pot stable
TOLERANCE_CODE = 100     # marge acceptée autour de chaque valeur du code
DUREE_STABLE_MS = 1000   # durée de stabilité requise pour valider un choix

position = 0
valeur_reference = pot.read()
debut_stable = time.ticks_ms()
attente_mouvement = False

print("Cadenas verrouillé. Entrez la combinaison.")

while True:
    v = pot.read()
    print(v)
    maintenant = time.ticks_ms()

    if abs(v - valeur_reference) > TOLERANCE_STABLE:
        valeur_reference = v
        debut_stable = maintenant
        attente_mouvement = False
    else:
        # Valeur stable : on vérifie si elle l'est depuis assez longtemps
        if (not attente_mouvement) and time.ticks_diff(maintenant, debut_stable) >= DUREE_STABLE_MS:
            print("Choix validé :", valeur_reference)
            if abs(valeur_reference - code[position]) <= TOLERANCE_CODE:
                position += 1
                print("Bonne valeur !", position, "/", len(code))
                if position == len(code):
                    print("*** Cadenas déverrouillé ! ***")
                    break
            else:
                print("Mauvaise valeur, la séquence est réinitialisée.")
                position = 0
            attente_mouvement = True

    time.sleep_ms(20)

```