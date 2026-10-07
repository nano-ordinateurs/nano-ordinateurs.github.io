# Python

Python est un langage interprété, cela veut dire qu'il n'y a pas d'étape de compilation avant l'exécution, contrairement à d'autres langages comme C. Donc la simplicité d'usage et le nombre de paquets disponibles en font un langage de premier plan pour les développeurs de tout type.

Dans le cadre du cours, on se limitera à l'usage de la bibliothèque standard et au paquet `sense_emu`.

## Ressources pour apprendre le langage

- [Le tutoriel Python](https://docs.python.org/fr/3/tutorial/)
- [Le shell interactif Python](https://ipython.org/) , vous pouvez simplement taper python dans votre terminal pour avoir un version simplifié.

## La bibliothèque standard

La bibliothèque standard désigne l'ensemble des fonctionnalités présentes dans Python de façon native. Voici quelques fonctionnalités :

## Gestion des paquets et environnements virtuels

Le grand attrait du langage Python est qu'il permet de télécharger des bibliothèques afin d'effectuer n'importe quelle tâche en seulement quelques lignes de code. En effet, un grand nombre de bibliothèques offrent des fonctions simples d'usage qui cachent en réalité une quantité énorme de code hautement optimisé (ce n'est pas toujours le cas, mais pour les bibliothèques populaires, ça l'est). Ces paquets peuvent être installés de deux façons : dans le système ou bien dans un environnement virtuel.

### Installation sur le système

On peut installer un paquet globalement directement avec :

```bash
pip install nom_du_paquet
```

Cependant, cette méthode comporte plusieurs inconvénients :

- Il peut y avoir des conflits entre versions de paquets.
- Il devient difficile de reproduire un projet d'une machine à une autre.
- L'environnement système peut être pollué (certain de vos autres projets peuvent ne plus fonctionner).

Donc il est généralement déconseillé d'installer des paquets globalement, sauf pour des paquets de base qui sont très populaires et bien maintenus. Donc, si vous voulez utiliser des paquets qui ne sont pas activement maintenue, il faut éviter de les installer globalement et plutôt privilégier un `venv`. 

### Installation dans un .venv

Un environnement virtuel est un répertoire qui contiendra l'ensemble des bibliothèques nécessaires au fonctionnement du projet. Chaque projet peut ainsi avoir ses propres dépendances dans un seul et même endroit sans interagir avec les autres projets ou le système. Cela a pour effet de limiter les dégâts possibles en cas d'erreur et de faciliter le débogage si on a un problème avec certains paquets.

C'est la méthode recommandée pour les projets Python.

#### Créer un .venv

Dans le dossier du projet :

```bash
python -m venv .venv
```

Cela va créer un environnement virtuel. Cet environnement doit ensuite être activé afin que le shell utilise la version de Python présente dans le répertoire et non la version présente dans le système.

```bash
source .venv/bin/activate
```

Vous pouvez maintenant télécharger les paquets dont vous avez besoin de manière plus sécuritaire avec `pip`.

```bash
python -m pip install nom_du_paquet
```