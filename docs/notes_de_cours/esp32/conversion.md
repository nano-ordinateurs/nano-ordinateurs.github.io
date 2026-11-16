# Conversion

De nombreux signaux ne sont pas numérique et nécessite un traitement particulier afin de pour les interprétés.

## Conversion analogique à numérique



Comme vu précedement, la sortie de notre potentiomètre est une tension électrique qui varie selon la position du curseur, entre `0 V` et `3,3 V`. Le potentiomètre monté entre `3V3` et `GND` permet ainsi de choisir le voltage en sortie grâce au curseur (la petite manivelle).

Mais une entrée numérique classique de l’ESP32 ne sait lire que deux états:

- `0 V` → état  `0` (`LOW`)
- `3,3 V` → état `1` (`HIGH`)

Heureusement, l’ESP32 possède plusieurs broches munies d’un convertisseur analogique à numérique, souvent appelé ADC (*Analog to Digital Converter*). On peut donc brancher directement la sortie du potentiomètre sur une broche compatible ADC. Il faut cependant porter une attention particulière au Pin-Out (graphe montrant la totalité des broches) afin de s'assurer que le port GPIO utilisé contient belle et bien un ADC.


Pour mieux comprendre la conversion analogique à numérique, nous allons étudier le code suivant (présent dans la section sur le micropython) :

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

Après avoir utilisé la fonction `ADC` pour associer le convertisseur à la broche choisie, deux configuration très importante sont faîtes. On choisit d’abord un facteur d’atténuation, puis une résolution pour la conversion.

- l'atténuation : la plage de tension maximum que l'ADC peut interprété. 
- la résolution : le nombre de bit utilisé pour représenter notre signal.


| Réglage | Constante MicroPython | Effet |
|---|---|---|
| Atténuation | `ADC.ATTN_0DB` | plage d’environ `0–1,1 V` |
| Atténuation | `ADC.ATTN_2_5DB` | plage d’environ `0–1,5 V` |
| Atténuation | `ADC.ATTN_6DB` | plage d’environ `0–2,2 V` |
| Atténuation | `ADC.ATTN_11DB` | plage d’environ `0–3,3 V` |
| Résolution | `ADC.WIDTH_9BIT` | valeurs de `0` à `511` |
| Résolution | `ADC.WIDTH_10BIT` | valeurs de `0` à `1023` |
| Résolution | `ADC.WIDTH_11BIT` | valeurs de `0` à `2047` |
| Résolution | `ADC.WIDTH_12BIT` | valeurs de `0` à `4095` |


On peut aussi faire le raisonnement dans l'autre sens pour estimer le voltage en sortie de notre potentiomètre. En pratique, cette lecture est rarement précise surtout lorsqu'on est proche des valeurs extrêment de l'esp32 (`0v` ou `3,3v`) mais souvent bien utile. 


```text
V_mesurée = valeur_ADC × V_plage / Max_résolution
```




## Conversion digital a analogue
