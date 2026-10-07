# Correction exercice 05

```py
from sense_emu import SenseHat
from time import sleep

sense = SenseHat()

modes = ["temperature", "humidity", "pressure"]
mode = 0

temp_max = 105.00
temp_min = -30.00

while True:

    for event in sense.stick.get_events():

        if event.action == "pressed":

            if event.direction == "right":
                mode += 1

            elif event.direction == "left":
                mode -= 1

            mode = mode % len(modes)

    # Affichage
    match modes[mode]:

        case "temperature":

            temperature = sense.get_temperature()

            print(f"Température : {temperature:.2f} °C")

            # Calcul du nombre de LED
            nb_leds = int(max(0, min(8,(temperature - temp_min)/(temp_max - temp_min) * 8)))

            pixels = []

            for y in range(8):
                for x in range(8):

                    if x == 0 and y >= 8 - nb_leds:
                        pixels.append((255, 0, 0))
                    else:
                        pixels.append((0, 0, 0))

            sense.set_pixels(pixels)

        case "humidity":
            humidity = sense.get_humidity()
            print(f"Humidité : {humidity:.2f} %")
            sense.show_letter("H")

        case "pressure":
            pressure = sense.get_pressure()
            print(f"Pression : {pressure:.2f} hPa")
            sense.show_letter("P")

    sleep(0.1)
```