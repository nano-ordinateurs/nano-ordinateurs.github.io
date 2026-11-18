# Correction exercice 8

## Question 1

```py

from machine import Pin, ADC
import time

pot = ADC(Pin(12))
pot.atten(ADC.ATTN_11DB)      # plage proche de 0 a 3,3 V
pot.width(ADC.WIDTH_12BIT)    # resolution 12 bits

del1 = Pin(15,Pin.OUT,Pin.PULL_DOWN)

slp = 1   # une seconde de clignotement a la base


def clignotement(val_pot, slp=1):
    
    # On decide de maniere arbitraire que 4095 multiplie le clignotement par 2
    # et 0 le divise par 2
    
    modificateur_sleep = (val_pot + 1000) / (4095/2) # On ajoute 1000 pour eviter un temps de clignotement qui tends vers 0
    new_slp = slp*modificateur_sleep
    
    print("valeur du potentiometre" + str(val_pot))
    print("valeur du nouveau sleep" + str(new_slp))
    
    time.sleep(new_slp)
    del1.on()
    time.sleep(new_slp)
    del1.off()

while True:
    
    clignotement(pot.read())    

```

## Question 2


```py
from machine import Pin, ADC
import time

pot = ADC(Pin(12))
pot.atten(ADC.ATTN_11DB)      # plage proche de 0 a 3,3 V
pot.width(ADC.WIDTH_12BIT)    # resolution 12 bits

del1 = Pin(15,Pin.OUT,Pin.PULL_DOWN)

slp = 1   # une seconde de clignotement a la base

seuil = (4095 / 2)


while True:
    
    if (pot.read() > seuil):
        del1.on()
    else:
        del1.off()
    
    time.sleep_ms(200)
    
```

## Question 3


```py

from machine import Pin, ADC
import time

pot = ADC(Pin(12))
pot.atten(ADC.ATTN_11DB)      # plage proche de 0 a 3,3 V
pot.width(ADC.WIDTH_12BIT)    # resolution 12 bits

dels = [Pin(15,Pin.OUT,Pin.PULL_DOWN), Pin(2,Pin.OUT,Pin.PULL_DOWN), Pin(0,Pin.OUT,Pin.PULL_DOWN)]

slp = 1   # une seconde de clignotement a la base

step = (4095 / 3)




while True:
        
    pot_val = pot.read()
    
    if (pot_val < step):
        dels[0].on()
        dels[1].off()
        dels[2].off()
    elif (pot_val < step*2):
        dels[0].off()
        dels[1].on()
        dels[2].off()
    elif (pot_val <= step*3):
        dels[0].off()
        dels[1].off()
        dels[2].on()
        
    time.sleep_ms(200)
    

```


## Question 4

```py
from machine import Pin
import time

btn = Pin(14, Pin.IN, Pin.PULL_DOWN)

# Vert -> Jaune -> Rouge
dels = [Pin(15, Pin.OUT), Pin(2, Pin.OUT), Pin(0, Pin.OUT)]


def off():
    for d in dels:
        d.value(0)


def passage_pieton():
    off()
    dels[1].value(1)          # Jaune
    time.sleep(2)
    off()
    dels[2].value(1)          # Rouge
    time.sleep(6)
    off()
"""
# Version avec un sleep
def feu_circulation():
    for i in range(3):
        off()
        dels[i].value(1)      
        # On attend 4s en surveillant le bouton toutes les 100 ms
        for _ in range(40):   # 40 x 100 ms = 4 s
            if btn.value() == 1:
                return True
            time.sleep_ms(100)
    return False
"""


# Version avec la fonction time.time()
def feu_circulation():
    for i in range(3):
        t1 = time.time()
        off()
        dels[i].value(1)      
        while (time.time() - t1) < 4:
            if btn.value() == 1:
                return True

    return False



while True:
    # On fait le cycle normal tant que le pieton n'appuie pas sur le bouton
    if feu_circulation():
        passage_pieton()

```

## Question 5

```py

from machine import Pin
import time

btn = Pin(14, Pin.IN, Pin.PULL_DOWN)

# l'incertitude qui sera tolerer
erreur = 0.5 # 0.5 seconde d'incertitude


code = [1, 3, 2]



etat_btn_t1 = 0
etat_btn_t2 = 0

compteur_code = 0
temps_appuie = 0

while compteur_code != len(code) :
    
    
    etat_btn_t2 = btn.value()
    
    # Nouvelle appuie
    if(etat_btn_t1 == 0 and etat_btn_t2 == 1):
        t1 = time.time()
        print("nouvel appuie detecter")

    # Arret d'un appuie
    elif(etat_btn_t1 == 1 and etat_btn_t2 == 0):
        if (time.time() - t1 > (code[compteur_code] - erreur) and time.time() - t1 < (code[compteur_code] + erreur)):
            compteur_code += 1
            print("Etape valide", compteur_code, "/", len(code))
        else:
            # on recommence si il y a une erreur dans la sequence
            compteur_code = 0
            print("Etape ecoucher retour a 0")
            
    etat_btn_t1 = etat_btn_t2
    
    time.sleep_ms(50)
    
    
        
    
print("Porte Ouverte!")    

```

## Question 6


```py

from machine import Pin, ADC
import time

btn1 = Pin(5, Pin.IN, Pin.PULL_DOWN)
btn2 = Pin(18, Pin.IN, Pin.PULL_DOWN)

ancien_btn1 = 0
nouveau_btn1 = 0

ancien_btn2 = 0
nouveau_btn2 = 0

pot1 = ADC(Pin(32))
pot1.atten(ADC.ATTN_11DB)
pot1.width(ADC.WIDTH_12BIT) 

pot2 = ADC(Pin(33))
pot2.atten(ADC.ATTN_11DB)
pot2.width(ADC.WIDTH_12BIT) 

last_val_pot1 = 0
last_val_pot2 = 0

operation = ["add", "sub", "mul", "div"]

compteur_op = 0

def calcul(num1, num2, op):
    
    if op == "add":
        return num1 + num2
    elif op == "sub":
        return num1 - num2
    elif op == "mul":
        return num1 * num2
    elif op == "div":
        return num1 / num2



while True:
    
    nouveau_btn1 = btn1.value()
    nouveau_btn2 = btn2.value()

    if abs(pot1.read() - last_val_pot1) > 10:
        print("Valeur pot1 :", pot1.read())
        
    if abs(pot2.read() - last_val_pot2) > 10:
        print("Valeur pot2 :", pot2.read())

    if( nouveau_btn1 == 1 and ancien_btn1 == 0):
        compteur_op += 1
        compteur_op = compteur_op % 4
        print("Operation choisie", operation[compteur_op])
    
    if(nouveau_btn2 == 1 and ancien_btn2 == 0):
        print("Resultat : ", operation[compteur_op], " ", pot1.read(), " ", pot2.read(), " = ", calcul(pot1.read(),pot2.read(),operation[compteur_op]))

    
    last_val_pot1 = pot1.read()
    last_val_pot2 = pot2.read()
    
    ancien_btn1 = nouveau_btn1
    ancien_btn2 = nouveau_btn2
    time.sleep_ms(200)

```

