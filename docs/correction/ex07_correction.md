# Exercice 07 - Boutons, potentiomètre et DELs



#### Question 2
```py
# Pour avoir un interrupter qui fonctionne comme un interrupteur commun
# ou on peut mettre une lumiere a On/off

from machine import Pin
import time

led = Pin(15, Pin.OUT)
etat_led = 0

bouton = Pin(4, Pin.IN, Pin.PULL_DOWN)

ancien_etat = 0


# tenir compte des etats permet de ne pas a avoir joueur avec la duree
# de sleep
while True:

    nouvel_etat = bouton.value()


    if nouvel_etat == 1 and ancien_etat == 0:
        etat_led = not etat_led
        led.value(etat_led)
        print("LED :", etat_led)


    ancien_etat = nouvel_etat
    time.sleep_ms(20)

```


### Question 4

```py
from machine import Pin
import time

bouton = Pin(4, Pin.IN, Pin.PULL_DOWN)

compteur = 0

# Etats du bouton
etat_bouton_ancien = 0
etat_bouton_nouveau = 0

while True:

    etat_bouton_nouveau = bouton.value()

    # Detection d'un nouvel appui
    if etat_bouton_nouveau == 1 and etat_bouton_ancien == 0:

        compteur += 1
        print("Nombre d'appuis :", compteur)

    etat_bouton_ancien = etat_bouton_nouveau

    time.sleep_ms(20)

```
### Question 5


```py
from machine import Pin
import time

bouton = Pin(4, Pin.IN, Pin.PULL_DOWN)


dels = [Pin(12,Pin.OUT, Pin.PULL_DOWN),Pin(13,Pin.OUT, Pin.PULL_DOWN),Pin(33,Pin.OUT, Pin.PULL_DOWN)]

compteur = 0

# Etats du bouton
etat_bouton_ancien = 0
etat_bouton_nouveau = 0


# La meilleur solution c'est d'utiliser les operateurs binaire
# Cependant faire des IF-ELIF est un solution equivalente.
def convertir(num):
    for i in range(3):
        bit = (num >> i) & 1
        dels[i].value(bit)

while True:
    etat_bouton_nouveau = bouton.value()

    # Detection d'un nouvel appui
    if etat_bouton_nouveau == 1 and etat_bouton_ancien == 0:

        compteur += 1
        print("Nombre d'appuis :", compteur)

    etat_bouton_ancien = etat_bouton_nouveau
    
    convertir(compteur)

    # on veut revenir a 0 apres 8
    compteur = compteur % 8
    time.sleep_ms(20)
```

### Question 6 (Bonus)

```py

from machine import Pin
import time


bouton = Pin(4, Pin.IN, Pin.PULL_DOWN)

# la duree 
slp = 100

point = 1 
tiret = 3

espace_symbole = 1
espace_lettre = 3 
espace_mot = 7 



# On cree un dictionnaire pour lire les lettres a partir 
morse_inverse = {
    (point, tiret): 'A',
    (tiret, point, point, point): 'B',
    (tiret, point, tiret, point): 'C',
    (tiret, point, point): 'D',
    (point,): 'E',
    (point, point, tiret, point): 'F',
    (tiret, tiret, point): 'G',
    (point, point, point, point): 'H',
    (point, point): 'I',
    (point, tiret, tiret, tiret): 'J',
    (tiret, point, tiret): 'K',
    (point, tiret, point, point): 'L',
    (tiret, tiret): 'M',
    (tiret, point): 'N',
    (tiret, tiret, tiret): 'O',
    (point, tiret, tiret, point): 'P',
    (tiret, tiret, point, tiret): 'Q',
    (point, tiret, point): 'R',
    (point, point, point): 'S',
    (tiret,): 'T',
    (point, point, tiret): 'U',
    (point, point, point, tiret): 'V',
    (point, tiret, tiret): 'W',
    (tiret, point, point, tiret): 'X',
    (tiret, point, tiret, tiret): 'Y',
    (tiret, tiret, point, point): 'Z',
    (tiret, tiret, tiret, tiret, tiret): '0',
    (point, tiret, tiret, tiret, tiret): '1',
    (point, point, tiret, tiret, tiret): '2',
    (point, point, point, tiret, tiret): '3',
    (point, point, point, point, tiret): '4',
    (point, point, point, point, point): '5',
    (tiret, point, point, point, point): '6',
    (tiret, tiret, point, point, point): '7',
    (tiret, tiret, tiret, point, point): '8',
    (tiret, tiret, tiret, tiret, point): '9'
}




etat_bouton_ancien = 0
etat_bouton_nouveau = 0

compteur = 0
compteur_espace = 0

code = ()
message = ""


while True:
    
    etat_bouton_nouveau =  bouton.value()
    
    if(etat_bouton_nouveau == 1):
        compteur += 1
        compteur_espace = 0
        
    else:
    
        # le bouton vient d'etre relache
        if(etat_bouton_ancien == 1):
            
            if compteur < tiret - 1:
                code += (point,)
                #print(".")
                
            else:
                code += (tiret,)
                #print("-")
            
            lettre = morse_inverse.get(code, "?")
            compteur = 0
        
        # ca fait plusieurs cycle que le bouton est relache
        else:
            compteur_espace += 1
            
            if compteur_espace == espace_lettre:
                lettre = morse_inverse.get(code, "?")
                message += lettre
                print(lettre)
                code = ()
            
            elif compteur_espace == espace_mot:
                message += " "
                #print("Espace entre mots")
            
    etat_bouton_ancien = etat_bouton_nouveau
    time.sleep_ms(slp)
```