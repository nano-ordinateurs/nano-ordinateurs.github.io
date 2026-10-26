# ESP32

Ce chapitre abordera la programmation de microcontrôleur à l'aide de MicroPython.

## Les microcontrôleurs

Les microcontrôleurs sont des puces électroniques programmables avec seulement quelques Mo de mémoire disponible et un processeur ayant une faible fréquence et peu de cœurs, ce qui limite les tâches pouvant être accomplies par celles-ci. Elles contiennent donc un processeur, de la mémoire RAM et de la mémoire Flash. En contrepartie, les microcontrôleurs sont généralement très peu coûteux, ce qui fait qu'ils sont idéaux pour les produits électroniques grand public où un coût unitaire faible est nécessaire afin de maximiser la marge. Cela a mené à l'utilisation des microcontrôleurs dans tous les aspects de la vie moderne (santé, équipement industriel, automobile, avionique, etc.).

Ces contraintes changent beaucoup de choses pour un développeur qui doit adapter ses outils, ses algorithmes et sa façon de travailler afin de faire des tâches dans un environnement où les ressources sont minimes. Il faut donc avoir une grande compréhension du fonctionnement des microcontrôleurs afin de faire un usage judicieux de ses outils.

Voici certaines spécificités propres au travail sur les microcontrôleurs :

- **Il n'y a pas d'OS au sens classique.**. Un programme « bare-metal » s'exécute directement sur le matériel, sans OS intermédiaire. Cependant, dans le cas de MicroPython sur ESP32, le firmware embarque un système d'exploitation temps réel (RTOS). Ce RTOS gère la concurrence, le Wi-Fi, le Bluetooth et l'exécution de vos scripts. Il faut donc réaliser certaines tâches faites par l'OS par nous-mêmes, mais pas toutes.
- **Un firmware est nécessaire et doit être flashé.** Le firmware est le logiciel gravé dans la mémoire Flash de la carte qui s'exécute à son démarrage. Pour utiliser MicroPython, il faut d'abord **flasher un firmware MicroPython** sur l'ESP32. Ce firmware contient l'interpréteur Python, les pilotes matériels et le RTOS. Ensuite, on téléverse le **programme** (le script Python) autant de fois que nécessaire.
- **Déboguer devient beaucoup plus difficile.** Souvent, déterminer l'origine du problème demande de faire une vraie chasse au trésor, puisque le problème peut être logiciel ou physique. Et même lorsque le problème est logiciel, il est difficile de déterminer à quel niveau il se situe.
- **On ne sait jamais réellement si un programme fonctionne tant qu'on ne l'a pas essayé sur un vrai microcontrôleur.** Les simulateurs ne reproduisent pas fidèlement le timing précis, le bruit électrique ou les imperfections des capteurs.
- **Les erreurs de programmation ne sont pas rattrapables dans certains cas.** Une erreur peut provoquer un plantage, corrompre des données ou même verrouiller la carte.

## Notre ESP32

Le ESP32 est un microcontrôleur versatile et peu coûteux qui est parfait pour apprendre à programmer des systèmes embarqués. Sa spécificité principale est de permettre d'avoir une antenne lui permettant de se connecter au Wi-Fi ainsi que de se connecter à une machine par Bluetooth.

Pour exécuter des programmes sur notre ESP32, nous avons d'abord besoin de charger un firmware. Le firmware, comme son nom l'indique, fait interface entre le hardware et le software. C'est-à-dire qu'il fait le pont entre les logiciels que nous allons développer et les ressources du microcontrôleur. Sans firmware, notre éditeur ne pourra pas se connecter à l'ESP32 car celui-ci ne saura simplement pas comment communiquer à l'aide du port USB-C.

Ce microcontrôleur est aussi muni de broches que nous allons utiliser afin de communiquer et d'alimenter en électricité des composants divers et variés. Certaines broches servent donc à alimenter des composants en électricité, d'autres à transmettre et lire des données, et certaines font les deux. Pour l'instant, il est important d'identifier les ports GPIO (general-purpose input output) ainsi que les ports fournissant du courant électrique et les ports menant à la terre. La compréhension du rôle de chaque broche est donc nécessaire afin d'être efficace pour la programmation de microcontrôleur, surtout lorsque la question de la communication entre composants électroniques est abordée.



| Catégorie | Spécification | Détails |
| :--- | :--- | :--- |
| **Processeur** | Microcontrôleur | ESP32-WROVER (double cœur Xtensa® 32-bit LX6)|
| | Fréquence d'horloge | Jusqu'à **240 MHz**|
| **Mémoire** | Flash | **4 Mo**[reference:2] |
| | PSRAM | **8 Mo** |
| | SRAM | 520 Ko |
| **Connectivité** | Wi-Fi | 802.11 b/g/n (2.4 GHz) |
| | Bluetooth | v4.2 avec BLE (Bluetooth Low Energy) |
| | Antenne | PCB intégrée |
| **Alimentation** | Tension de fonctionnement | 3.3V |
| | Alimentation | Via port USB (5V) ou broche VIN |
| **Périphériques intégrés** | Caméra | Connecteur pour module caméra (OV2640/OV3660) |
| | Lecteur de carte | Emplacement pour carte microSD |
| | LED intégrée | Connectée à **GPIO2** |
| | Boutons | **EN (Reset)** et **BOOT (GPIO0)** |
| **Broches (Pinout)** | GPIO | Nombreuses broches d'E/S numériques (ADC, PWM, I2C, SPI, UART) |
| | I2C (défaut) | GPIO13 (SDA) et GPIO14 (SCL) |
| | UART (REPL) | GPIO1 (TX) et GPIO3 (RX) - **occupées par le REPL** |
| | Broches à éviter | GPIO6 à GPIO11 (connectées à la Flash SPI interne) |
| | Strapping Pins | GPIO0, GPIO2, GPIO5, GPIO12, GPIO15 (rôle au démarrage) |
| **Température** | Plage de fonctionnement | -40 °C ~ 85 °C|

![Diagramme de bloc du ESP32](/assets/images/Espressif_ESP32_Chip_Function_Block_Diagram.svg){.center .shadow}

### Micropython

MicroPython

MicroPython est en fait une version allégée de Python qui est conçue pour fonctionner directement sur des microcontrôleurs comme notre ESP32. Concrètement, c'est un interpréteur Python qui est embarqué directement dans le firmware de la carte, c'est-à-dire que c'est la carte elle-même qui exécute le code Python. Cet outil a beaucoup d'avantages. D'abord, il inclut un petit système d'exploitation en temps réel (un RTOS, nous verrons l'utilité d'un tel outil plus tard dans la session), ce qui lui permet de gérer plusieurs tâches en parallèle, comme le Wi-Fi, le Bluetooth et l'exécution de votre script en même temps. De plus, MicroPython permet de gérer directement nos ressources matérielles à l'aide de trois modules principaux :

- Le module **machine** qui permet de gérer les broches (GPIO) de notre ESP32.

- Le module **network** qui permet d'utiliser le Bluetooth et le Wi-Fi de la carte.

- Le module **time** qui permet de dormir ou bien encore de gérer des horloges.

Un autre avantage important est le REPL (Read-Eval-Print Loop). C'est une console interactive qui permet de taper une commande et de voir le résultat immédiatement, sans avoir à téléverser un programme complet. C'est un outil extrêmement pratique pour tester une broche, vérifier la valeur d'un capteur ou comprendre une erreur en direct, ce qui rend le débogage beaucoup plus simple.



### Documentation



La documentation est disponible ici : [Doc](https://docs.freenove.com/projects/fnk0060/en/latest/fnk0060/codes/Python/Welcome.html).


Freenove kit doc:  https://freenove.com/tutorial

Port USB-C -> bien entrer le connecteur, courant peut être connecté sans que les donneés soient fonctionnelles

Boutons sur le kit Freenove:

- Reset -> relance le programme
- Boot -> bootstrap mode: laisser Boot appuyé, appuyer sur Reset, relacher Boot



