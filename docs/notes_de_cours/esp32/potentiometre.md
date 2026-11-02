# Le potentiomètre

Le potentiomètre est un capteur qui permet d'appliquer une résistance variable sur une broche et en conséquence une lecture de tension qui varie continuellement avec la position du contrôle. La valeur sera déterminée par selon un angle compris entre 0 (du côté du *ground*) et 275 degrées.

## Branchement

Le capteur comprends trois broches. Si on met le potientomètre à plat, avec le "bras" orienté vers le haut et les broches tournées vers nous, les branchements sont les suivants : 

- À gauche : La mise à la terre
- Au milieu : La sortie qu'on connecte à un GPIO du ESP32
- À droite : L'alimentation 3.3V

![pot_01.png](\assets\images\pot_01.png){.center}



