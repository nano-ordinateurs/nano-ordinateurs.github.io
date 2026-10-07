# Correction exercice 04


Pour la version ou le joystick déclanche un lancer : 

```py
from sense_emu import SenseHat
from time import sleep
from random import randint

sense = SenseHat()

while True:
    for event in sense.stick.get_events():
        if event.direction == "middle" and event.action == "pressed":
            sense.show_letter(str(randint(1, 6)))
            sleep(1)
            sense.clear()

    sleep(0.05)   # petite pause pour ne pas saturer le CPU

```



Pour la version ou le mouvement déclanche un lancer :

```py
# Ce programme permet de simuler un lancer de des declancher par un mouvement du sense_emu_gui.
#
# Contrairement a la version montrer en classe j'ai ajouter un "cooldown" afin d'eviter que l'affichage change 
# sans mouvement. 
#
# Petite subtilite, la fonction get_accelerometer_raw() ne lit  pas les variations de gyroscope sur l'axe Z (yaw)


from sense_emu import SenseHat
from time import sleep, time
from random import randint

sense = SenseHat()

old_accel = sense.get_accelerometer_raw()

COOLDOWN = 2.0          # secondes minimum entre deux lancers
last_trigger = 0        # timestamp du dernier lancer


while True:

    accel = sense.get_accelerometer_raw()
    now = time()

    if (abs(accel["x"] - old_accel["x"]) > 1 or
        abs(accel["y"] - old_accel["y"]) > 1):

        if now - last_trigger > COOLDOWN:
            sense.show_letter(str(randint(1, 6)))
            last_trigger = now
            sleep(1)

            accel = sense.get_accelerometer_raw()

    old_accel = accel
    sleep(0.3)


```