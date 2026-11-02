# Exercice 07 - Boutons, potentiomètre et DELs

Dans cette exercice on cherche a approfondir votre connaissance du micropython en réalisant des opération de lecture et d'écriture.



1. Sans écrire de programme, réalisez un circuit qui allume une DEL lorsqu’on appuie sur un bouton poussoir.

2. Maintenant créer un programme micropython et faite un branchement mais cette fois-ci liser l'entrée a partir d'un port qui fait la même chose.

3. Sans faire de programme, utiliser un potentiometre pour faire briller une DEL plus fort ou moins fort.

4. Faite un programme qui vous permet de compter le nombre de fois qu'on appuie sur un bouton poussoir.

5. Créez un compteur qui affiche, à l’aide de plusieurs DEL, le nombre de fois où on a appuyé sur le bouton. L'affichage doit être binaire et votre circuit doit être capable de représenter au moins 8 chiffres (3 DEL devront être utilisé). 


(bonus)
6. Traducteur de code Morse avec un bouton

Crée un programme qui traduit le code Morse saisi à l’aide d’un bouton poussoir.

- **Entrée** : un bouton poussoir (appuis courts et longs pour les points et les traits).
- **Sortie** : affichage du code Morse ou du texte décodé dans la console.
- **Référence** : [Code Morse international — Wikipédia](https://fr.wikipedia.org/wiki/Code_Morse_international)

![Code Morse international](https://thumb.wikimedia.org/wikipedia/commons/thumb/8/8e/International_Morse_Code-fr.svg/960px-International_Morse_Code-fr.svg.png)

> Astuce : prévoyez un anti-rebond et une temporisation pour distinguer un point (appui court) d’un trait (appui long).