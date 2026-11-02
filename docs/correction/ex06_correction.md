Vous devez faire clignoter la DEL du ESP32 selon la fréquence que vous désirez.

```py
from machine import Pin
import time

led = Pin(2, Pin.OUT)   # la del est au port GPIO 2

while True:
    led.value(1)      # allumer la DEL
    time.sleep(1)
    led.value(0)      # eteindre la DEL
    time.sleep(1)
```
Faites un montage qui alimente en permanence une DEL.

```
Il suffisait de brancher une DEL sur le 5v en ayant mis une resistance pour ne pas qu'elle brule.
```

Faites un montage et un programme qui font clignoter une DEL.

```py
from machine import Pin
import time

led = Pin(2, Pin.OUT)   # la del est au port GPIO 2

while True:
    led.value(1)      # allumer la DEL
    time.sleep(1)
    led.value(0)      # eteindre la DEL
    time.sleep(1)
```



Faites clignoter deux DEL en alternance.

Nos dels sont brancher sur les ports gpio 0 et 15
```py
from machine import Pin
import time

led1 = Pin(0, Pin.OUT, Pin.PULL_UP)
led2 = Pin(15, Pin.OUT, Pin.PULL_UP)

while True:
    led1.on()
    led2.off()
    time.sleep(2)
    led2.on()
    led1.off()
    time.sleep(2)
```
Certains d'entre vous l'on fait avec le port GPIO 2 et la DEL de l'esp32. Pourquoi est-ce que ca marchais?



Faites un programme qui vous permet d’activer une DEL et d’en désactiver une autre selon votre volonté.
```py
from machine import Pin
import time

led1 = Pin(0, Pin.OUT)
led2 = Pin(15, Pin.OUT)
```
On peut aussi interagir avec notre programme directement avec le REPL (le terminal), il suffit de laisser le programme prendre fin. Cette fonctionnalite est essentiel pour debugger.
