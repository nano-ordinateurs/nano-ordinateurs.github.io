# Notre premier programme

## Un peu de mise en contexte

Après avoir flashé notre ESP32 avec le firmware nécessaire, on est prêt à écrire notre premier programme.

## Structure de nos programmes

La structure de nos programmes sera sensiblement toujours la même. En Python, nous ne sommes pas vraiment obligés de la respecter, mais cela fait partie des bonnes pratiques. Pour des programmes simples, on peut simplement écrire notre code de haut en bas sans structure complexe :

1. En premier, on va importer les bibliothèques requises et déclarer les variables et objets globaux (par exemple : `import machine`, `import time`, `led = machine.Pin(2, machine.Pin.OUT)`).
2. Ensuite, on place le code d'initialisation directement au début du script (équivalent du `setup()`), qui sera exécuté une seule fois au démarrage.
3. Finalement, on utilise une boucle de traitement principale avec `while True:` (équivalent du `loop()`) qui va tourner en boucle tant que l'ESP32 est sous tension.
4. On peut aussi déclarer des fonctions personnalisées avec `def` avant la boucle principale pour mieux structurer notre code.

## REPL
Il peut aussi être intéressant de créer un programme qui se termine afin de pouvoir interagir directement avec la carte via la console REPL.

## Premier petit test

Juste un petit programme tout mignon pour s'assurer que tout fonctionne. Dans Thonny, on a accès à un moniteur série dans lequel on peut lire du texte provenant de l'ESP32. C'est pratique pour déboguer ou simplement envoyer des communications. Faisons un script qui va y inscrire du texte en continu.

1. Créez un nouveau programme.
2. Inscrivez le code suivant :

```py
while True:
    print("hello world")
```

## Enfin un vrai programme

Maintenant, on va essayer de faire clignoter une petite DEL qui se situe sur l'ESP32.

![premier_sketch_01](/assets/images/premier_sketch_01.png){.center .shadow}

La DEL est associée au GPIO 2. Il faut commencer par l'indiquer dans notre code en créant une variable contenant la configuration de la broche. En fait, cette étape permet de simplifier le code quand on travaille avec plusieurs composants.

`led = machine.Pin(2, machine.Pin.OUT)`

!!! note

        Les GPIO (General-Purpose Input/Output) sont des connecteurs, ou broches (*pins*), qui nous permettent de connecter l'ESP32 à un circuit électrique. On peut les visualiser comme des interrupteurs qui peuvent basculer entre l'état "On" et "Off".

- Allumer la DEL : `led.on()` (ou `led.value(1)`)
- Éteindre la DEL : `led.off()` (ou `led.value(0)`)

**À vous de jouer**

Écrivez un script qui va faire clignoter la DEL en continu à un intervalle d'une seconde et demie. Quand la lumière s'allume, inscrivez "On" dans le moniteur série, et "Off" quand elle s'éteint.

**Ça fonctionne ?**

Maintenant que vous avez une belle petite lumière qui clignote, modifiez votre code pour qu'après 10 clignotements, la DEL s'éteigne et que le moniteur série affiche le message : "Ok c'est bon on a compris...". La DEL restera ensuite éteinte à moins qu'on redémarre l'ESP32.
