# Exercice 08 — Lecture ADC et formatif



## Question 1

Faites en sorte que la fréquence de clignotement de la DEL soit contrôlée par un potentiomètre. Autrement dit, la valeur lue sur la broche centrale du potentiomètre doit influencer la vitesse de clignotement de la DEL.


## Question 2

Créez un système qui permet de déterminer quand la DEL doit s’allumer en fonction de la valeur du potentiomètre.



## Question 3

Réalisez un montage permettant de lire la sortie d’un potentiomètre. Connectez ensuite 3 DEL à l’ESP32.

Concevez un programme qui allume une DEL en fonction de la sortie du potentiomètre. La sortie du potentiomètre varie de 0 à 4095. Cette plage doit être divisée en trois parties :

- entre 0 et 4095/3 : la DEL 1 est allumée ;
- entre 4095/3 et 2 × 4095/3 : la DEL 2 est allumée ;
- entre 2 × 4095/3 et 4095 : la DEL 3 est allumée.


## Question 4

Simulez un feu de circulation en utilisant 3 del (vert, jaune, rouge) qui est muni d’un bouton permettant de faire passer un piéton au rouge. En d'autre termes, 

- Chaque lumière doit rester allumée pendant exactement 4 secondes.
- Si on appuie sur le bouton, on passe au orange pendant 2 secondes, puis au rouge pendant 6 secondes, avant de reprendre le fonctionnement normal du programme.


## Question 5

Imaginez une porte munie d’une sonnette spéciale qui permet d’ouvrir la porte si on appuie selon un code secret.

Répliquez le fonctionnement de cette porte avec un circuit simple utilisant un bouton poussoir.

Le format du code secret est défini par un tableau :

```py
[1, 3, 2]
```

Ici, le code `[1, 3, 2]` signifie que l’utilisateur doit appuyer sur le bouton pendant 1 seconde, puis pendant 3 secondes, et enfin pendant 2 secondes afin d’ouvrir la porte. Quand l'utilisateur entre le bon code, imprimé : 'Porte ouverte' dans le terminal.


## Question 6

Pour Noël, vous décidez de fabriquer une guirlande de DEL artisanale. Composez un arrangement de 5 DEL qui clignotent de manière aléatoire. La fréquence de clignotement doit également être aléatoire.

## Question 7

On décide de fabriquer une calculatrice particulière permettant d’effectuer des opérations entre deux nombres.

Deux potentiomètres servent à choisir les deux nombres : le premier nombre est contrôlé par le premier potentiomètre, et le deuxième nombre par le second potentiomètre. Les deux valeurs sont comprises entre 0 et 4095.

Un premier bouton poussoir permet de choisir le type de calcul : addition, soustraction, division ou multiplication. Un second bouton poussoir permet de lancer le calcul.

Puis imprimer le résultat dans le terminal.

(Vous pouvez ignorer les cas spéciaux comme la division par 0).