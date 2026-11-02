# Micropython


Comme vu précédemment, **MicroPython** est une version allégée de Python dont le but premier est la programmation de microcontrôleurs et l'interaction en temps réel avec ceux-ci. Un grand nombre de bibliothèques conventionnelles de Python sont présentes dans MicroPython (`os`, `random`, `math`, `json`, etc.). À celles-ci s'ajoutent des bibliothèques spécifiques donc la plus utile est **`machine`** pour l'ESP32.


La liste complète et officielle est dans le [Python Module Index](https://docs.micropython.org/en/latest/py-modindex.html) de MicroPython. Cependant, cette il faut faire attention car les biblothèques peuvent varier en fonction du firmware qui est installé.

---

## Programmer des broches

La programmation des broches (GPIO) sur ESP32 est l'opération de base de toute les composant matériel, qu'ils soit electrique ou electronique. MicroPython introduit une abstraction qui simplifie grandement la vie qui nous permet de définir des broches directement dans le programme et interagir avec elles via la bibliothèque **`machine`**.


Dans Micropython, pour être capable de manipuler un objet, il faut d'abord tout d'abord avoir un objet de type `Pin` sur lequel appeler les fonctions appropriés. Pour ce faire, on utilise le constructeur présent dans la bibliothèque machine qui retourne des objets de type `pin`.

```py
pin = machine.Pin(id, mode=-1, pull=-1, *, value=None, drive=0, alt=-1)

```

https://docs.micropython.org/en/latest/library/machine.Pin.html

Porter une attention particulière a la variable `pull`.

---

## Signal logique vs analogique

La définition formel d'un signale est la suivante : c'est une grandeur physique qui varie dans le temps et qui transporte de l'information. Dans notre cas précis, le courant électrique que nous pouvons transmettre ou bien recevoir est un signal qui nous permet de transférer de l'information. 

On sépare les signaux deux type, un signal est soit  **logique** ou analogique **analogique**. 



| Critère | Signal analogique | Signal logique |
|---|---|---|
| Définition | Signal qui varie de façon continue | Signal qui prend un nombre limité d’états discrets |
| Valeurs | Infinité de valeurs possibles dans une plage | Généralement deux états : bas/haut, 0/1, faux/vrai |
| Variation | Continue dans le temps | Change par paliers, entre des niveaux définis |
| Représentation | Courbe continue | Créneaux, niveaux haut et bas |
| Précision | Dépend du bruit, de la tolérance et de la mesure | Dépend des seuils de tension définis |
| Exemples | Tension d’un potentiomètre, son, température analogique | Bouton poussoir, sortie d’une porte logique |
| Composants typiques | composant électrique | composant électronique|


## Les résistances de tirage

La résistance de tirage, appelée pull-up ou pull-down en anglais, permet d’imposer un niveau logique connu sur une entrée, notamment lorsqu’elle serait sinon laissée « flottante » ou sensible à du bruit qui pourrait faire varier sa valeur entre 1 et 0. Dans notre cas, cette résistance est configurable et sert en pratique à définir l’état logique de repos de l’entrée. Par exemple, avec un pull-up, une entrée reliée à un bouton ouvert est lue à l’état haut (1), lorsqu’on appuie sur le bouton, elle passe à l’état bas (0). On peut alors décider dans le programme que cet appui correspond à ON() et allume une DEL.

----

## Exemple de lecture et écriture pour différent composant

### Lecture d’un signal logique (bouton)

Un bouton poussoir fournit un signal qui vaut 1 ou 0.  
Pour éviter une entrée « flottante », on utilise une résistance de tirage interne **pull-up**. 

**Branchement**

| Élément | Broche |
|---|---|
| Bouton – entrée du bouton | GPIO4 |
| Bouton – sortie du bouton | GND |

**Code MicroPython**

```python
from machine import Pin
import time

bouton = Pin(4, Pin.IN, Pin.PULL_UP)

while True:
    etat = bouton.value()
    if etat == 0:
        print("Bouton appuye")
    else:
        print("Bouton relache")
    time.sleep_ms(100)
```

**Explication**

- `Pin(4, Pin.IN, Pin.PULL_UP)` configure la broche 4 en entrée avec un tirage au niveau haut.
- `bouton.value()` renvoie la valeur actuelle du bouton. Puisque la résistance est en mode pull-up, alors la valeur de base est de 1.

---

### Lecture d’un signal analogique (potentiomètre)

Un potentiomètre fournit une tension variable. L’ESP32 la convertit en valeur numérique à l’aide de son convertisseur analogique-numérique (ADC).

**Branchement**

| Élément | Broche |
|---|---|
| Potentiomètre – Broche gauche | GND |
| Potentiomètre – Broche du milieu (curseur) | GPIO34 (ADC) |
| Potentiomètre – Broche de droite 2 | 3,3 V |

**Code MicroPython**

```python
from machine import Pin, ADC
import time

pot = ADC(Pin(34))
pot.atten(ADC.ATTN_11DB)      # plage proche de 0 a 3,3 V
pot.width(ADC.WIDTH_12BIT)    # resolution 12 bits

while True:
    valeur = pot.read()       # 0 a 4095
    print("Valeur lu :", valeur)
    time.sleep_ms(100)
```

**Explication**

- `ADC(Pin(34))` crée un objet de lecture analogique sur la broche 34.
- `pot.atten(ADC.ATTN_11DB)`  règle l’atténuation pour mesurer jusqu’à environ 3,3 V.
- `pot.width(ADC.WIDTH_12BIT)` fixe la résolution à 12 bits donc la valeur lue est comprise entre 0 et 4095.
- `pot.read()` renvoie la valeur brute. Elle augmente quand on tourne le potentiomètre vers le 3,3 V et diminue vers GND.

---

### Écriture d’un signal logique (DEL)

Une DEL commandée par une broche numérique s’allume ou s’éteint selon le niveau logique écrit sur la broche.

**Branchement**

| Élément | Broche |
|---|---|
| DEL – anode (patte longue) | GPIO2 |
| DEL – cathode (patte courte) | GND |

**Code MicroPython**

```python
from machine import Pin
import time

led = Pin(2, Pin.OUT)

while True:
    led.value(1)      # allumer la DEL
    time.sleep(1)
    led.value(0)      # eteindre la DEL
    time.sleep(1)
```

**Explication**

- `Pin(2, Pin.OUT)` configure la broche 2 en sortie.
- `led.value()` impose un niveau haut : la DEL s’allume.

