---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.4
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---
# TP : Jeu du tic-tac-toe


Pour programmer, vous pouvez soit utiliser Pyzo, soit [Basthon](https://notebook.basthon.fr/?from=https://raw.githubusercontent.com/tcanta/itc2a/master/eleve/tp_morpion-eleves.ipynb).

Commandes Basthon :

- Ctrl + Entrée : exécuter la cellule
- Shift + Entrée : exécuter la cellule puis passer à la suivante
- Entrée : passer en mode édition
- Échap : sortir du mode édition

Les commandes suivantes sont valables uniquement hors du mode édition :

- A : créer une nouvelle cellule (en haut)
- B : créer une nouvelle cellule (en bas)
- D D : supprimer la cellule



## Présentation

Dans le jeu du tic-tac-toe (ou morpion), deux joueurs doivent remplir chacun à leur tour une case de la grille avec le symbole qui leur est attribué : X (joueur 1) ou O (joueur 2). Le gagnant est celui qui arrive à aligner trois symboles identiques, horizontalement, verticalement ou en diagonale. Le joueur 1 commence.

<center><img src=https://upload.wikimedia.org/wikipedia/commons/3/33/Tictactoe1.gif width=100></center>

On représente une grille de morpion par une matrice (liste de listes) $3\times 3$ contenant uniquement 0, 1, 2 :
- 0 : case libre  
- 1 : croix posée par le joueur 1
- 2 : rond posée par le joueur 2

## Dictionnaire et fonction de hachage

Nous aurons besoin d'utiliser un dictionnaire dont les clés sont des matrices (grilles de morpion). Cependant, nous avons vu dans le cours sur les dictionnaires que, étant représenté par table de hachage, un dictionnaire doit avoir des clés hachables. Un type mutable (modifiable) comme une liste (ou un tableau numpy) n'étant pas hachable, il n'est donc pas possible d'en utiliser comme clé d'un dictionnaire :


```python
d = { [1, 2, 3] : 4 } # on ne peut pas utiliser une liste comme clé d'un dictionnaire
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    Cell In[1], line 1
    ----> 1 d = { [1, 2, 3] : 4 } # on ne peut pas utiliser une liste comme clé d'un dictionnaire


    TypeError: unhashable type: 'list'


À la place, nous allons utiliser le type `L` suivant qui possède les mêmes opérations que les listes mais qui définit une fonction de hachage (en convertissant en tuple, qui est immutable). Le code ci-dessous utilise des notions de programmation objet largement hors-programme et n'est donc pas à comprendre.


```python
class L(list):
    # Methode de classe permettant de renvoyer un tuple représentant l'instance courante
    def to_tuple(self):
        def aux(l):
            # Si l est une liste
            if isinstance(l, list):
                # On cast en tuple ses élements recursivement, List étant un type récursif
                return tuple(map(aux, l))
            return l
        return aux(self)
    # On définit une methode de hachage en se basant sur la représentation en tuple de notre liste
    # C'est monInstanceDeL.__hash__() qui est appellée lorsque je demande hash(monInstanceDeL)
    def __hash__(self):
        return hash(self.to_tuple())
```

En pratique, on définira donc une grille de morpion avec `L(...)` :


```python
g_vide = L([[0, 0, 0], [0, 0, 0], [0, 0, 0]]) # grille initialement vide
g_vide
```




    [[0, 0, 0], [0, 0, 0], [0, 0, 0]]



On peut ensuite utiliser toutes les opérations sur des listes/matrices, comme d'habitude.

**Question** : Définir une variable `g1` représentant le morpion ci-dessous.  

```
X|O|
 | |
 | |
```


```python
g1 = L([[1, 2, 0], [0, 0, 0], [0, 0, 0]])
```

Pour afficher une grille, on pourra utiliser la fonction suivante.


```python
def afficher(g):
    for i in range(len(g)):
        for j in range(len(g[0])):
            if g[i][j] == 0:
                print(" ", end="")
            elif g[i][j] == 1:
                print("X", end="")
            else:
                print("O", end="")
            if j < 2:
                print("|", end="")
        print()
```


```python
afficher(g1)
```

    X|O|
     | |
     | |


**Question** : Écrire une fonction `cases_libres(g)` renvoyant la liste des positions `(i, j)` des cases vides de `g` (c'est-à-dire telles que `g[i][j]` vaut $0$).


```python
def cases_libres(g):
    cases = []
    for i in range(3):
        for j in range(3):
            if g[i][j] == 0:
                cases.append((i, j))
    return cases
```


```python
cases_libres(g1)
```




    [(0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]



**Question** : Écrire une fonction `joueur(g)` renvoyant le numéro du joueur qui doit jouer le prochain coup sur la grille `g`. On pourra compter le nombre de cases libres, par exemple. On rappelle que le joueur 1 commence.


```python
def joueur(grille):
    nb_cases_libres = len(cases_libres(grille))
    nb_cases_vides = 9 - nb_cases_libres
    if nb_cases_vides % 2 == 0:
        return 1
    else:
        return 2
```


```python
joueur(g1)
```




    1



**Question** : Écrire une fonction `gagnant(g)` renvoyant 1 si le joueur 1 est gagnant sur la grille `g` (c'est-à-dire si trois 1 sont alignés sur la grille), 2 si le joueur 2 est gagnant et 0 s'il n'y a pas encore de gagnant.  
On pourra utiliser `if ... == ... == ...:` pour tester si trois valeurs sont égales.


```python
def gagnant(g):
    #test des lignes et colonnes
    for i in range(3):
        if g[i][0] == g[i][1] == g[i][2] and g[i][0] != 0:
            return g[i][0]
        if g[0][i] == g[1][i] == g[2][i] and g[0][i] != 0:
            return g[0][i]
    #test des diagonales
    if g[0][0] == g[1][1] == g[2][2] and g[0][0] != 0:
        return g[0][0]
    if g[0][2] == g[1][1] == g[2][0] and g[0][2] != 0:
        return g[0][2]
    return 0
```


```python
afficher(g1)
print(gagnant(g1))

g2 = [[1, 2, 2], [0, 1, 0], [0, 0, 1]]
afficher(g2)
print(gagnant(g2))
```

    X|O|
     | |
     | |
    0
    X|O|O
     |X|
     | |X
    1


**Question** : Écrire une fonction `successeurs(g)` renvoyant la liste `next_l` des grilles que l'on peut obtenir à partir de `g` en jouant un coup.  
Pour chaque coup possible, plutôt que modifier `g`, on en fera une copie (`m = copy.deepcopy(g)` après avoir importé avec `import copy`) que l'on modifiera pour jouer un coup et que l'on ajoutera à la liste `next_l`.


```python
import copy

def successeurs(g):
    next_l = []
    joueur_actuel = joueur(g)
    for i, j in cases_libres(g):
        m = copy.deepcopy(g)
        m[i][j] = joueur_actuel
        next_l.append(m)
    return next_l
```


```python
afficher(g1)
successeurs(g1)
```

    X|O|
     | |
     | |





    [[[1, 2, 1], [0, 0, 0], [0, 0, 0]],
     [[1, 2, 0], [1, 0, 0], [0, 0, 0]],
     [[1, 2, 0], [0, 1, 0], [0, 0, 0]],
     [[1, 2, 0], [0, 0, 1], [0, 0, 0]],
     [[1, 2, 0], [0, 0, 0], [1, 0, 0]],
     [[1, 2, 0], [0, 0, 0], [0, 1, 0]],
     [[1, 2, 0], [0, 0, 0], [0, 0, 1]]]



**Question** : Écrire une fonction `attracteurs(g)` renvoyant la liste des grilles qui sont des attracteurs pour le joueur 1, sachant que `g` est la grille initiale. Une grille `v` est un attracteur si on a l'un des cas suivantes :
- le joueur 1 est gagnant sur la grille `v`
- c'est au joueur 1 de jouer et il existe un successeur de `v` qui soit un attracteur pour le joueur 1
- c'est au joueur 2 de jouer et tous les successeurs de `v` sont des attracteurs pour le joueur 1

Sinon (si c'est le joueur 2 qui gagne, ou qu'il y a match nul, par exemple), `v` n'est pas un attracteur (`d[v] = False` dans le code ci-dessous).

On pourra compléter la fonction suivante, par mémoïsation :


```python
def attracteurs(g):
    d = {} # d[v] = True si la grille v est attracteur pour le joueur 1
    def aux(v): # renvoie True si v est attracteur pour le joueur 1
        if v not in d:
            ... # calculer d[v] en utilisant la récurrence ci-dessus
        return d[v]
    aux(g)
    return [v for v in d if d[v]] # grilles v telles que d[v] == True
```


```python
def attracteurs(g):
    d = {}
    def aux(v):
        if v not in d:
            succ = [aux(w) for w in successeurs(v)]
            j = gagnant(v)
            if j == 1:
                d[v] = True
            elif j == 2 or len(succ) == 0:
                d[v] = False
            elif joueur(v) == 1:
                d[v] = any(succ)
            else:
                d[v] = all(succ)
        return d[v]
    aux(g)
    return [v for v in d if d[v]]
```


```python
afficher(g1) # grille initiale
for v in attracteurs(g1)[:5]: # affiche quelques attracteurs
    afficher(v)
```

    X|O|
     | |
     | |
    X|O|X
    O|X|O
    X|O|X
    X|O|X
    O|X|O
    X|O|
    X|O|X
    O|X|O
    X|X|O
    X|O|X
    O|X|O
    X| |O
    X|O|X
    O|X|O
    X| |


**Question** : Le joueur 1 a t-il une stratégie gagnante à partir de la grille `g1` ?

**Question** : Modifier `attracteurs(g)` en une fonction `strategie(g)` renvoyant un dictionnaire `d` contenant une stratégie gagnante pour le joueur 1 à partir de la grille `g`. Si `v` est une grille accessible depuis `g` :
- Si le joueur 1 a gagné sur la grille `v`, `d[v]` vaudra `True`.
- S'il n'y a pas de stratégie gagnante depuis `v`, `d[v]` vaudra `False`.
- Sinon, `d[v]` correspond à un coup possible pour le joueur 1 à partir de `v` correspondant à une stratégie gagnante.  


```python
def strategie(g):
    d = {}
    def aux(v):
        if v not in d:
            succ = successeurs(v)
            if gagnant(v) == 1:
                d[v] = True
            elif gagnant(v) == 2 or len(succ) == 0:
                d[v] = False
            elif joueur(v) == 1:
                d[v] = False
                for w in succ:
                    if aux(w):
                        d[v] = w
            else:
                d[v] = all([aux(w) for w in succ])
        return d[v]
    aux(g)
    return d
```


```python
d = strategie(g1)
afficher(g1)
afficher(d[g1])
```

    X|O|
     | |
     | |
    X|O|
     | |
    X| |


**Question** : Trouver une séquence permettant de gagner contre l'ordinateur avec la fonction suivante (vous êtes le joueur 2).


```python
def jeu(g):
    d = strategie(g)
    while gagnant(g) == 0:
        if joueur(g) == 2:
            afficher(g)
            i, j = map(int, input(f"{g}\nEntrez les coordonnées de votre coup : ").split())
            g[i][j] = 2
        else:
            if d[g] == False:
                print("Pas de stratégie gagnante")
                return
            g = d[g]
    print("Le joueur", gagnant(g), "a gagné !")
    afficher(g)

jeu(g1)
```

    X|O|
     | |
    X| |


    [[1, 2, 0], [0, 0, 0], [1, 0, 0]]
    Entrez les coordonnées de votre coup :  0 1


    X|O|
     | |
    X| |


    [[1, 2, 0], [0, 0, 0], [1, 0, 0]]
    Entrez les coordonnées de votre coup :  0 0


    O|O|
     | |
    X| |


    [[2, 2, 0], [0, 0, 0], [1, 0, 0]]
    Entrez les coordonnées de votre coup :  0 2


    Le joueur 2 a gagné !
    O|O|O
     | |
    X| |


**Question** : Améliorez la fonction jeu au regard de la question précédente


```python
def jeu(g):
    d = strategie(g)
    while gagnant(g) == 0:
        if joueur(g) == 2:
            afficher(g)
            i, j = map(int, input(f"{g}\nEntrez les coordonnées de votre coup : ").split())
            if g[i][j]==0:
                g[i][j] = 2
            else:
                print("Coup invalide, tricher c'est mal !")
        else:
            if d[g] == False:
                print("Pas de stratégie gagnante")
                return
            g = d[g]
    print("Le joueur", gagnant(g), "a gagné !")
    afficher(g)

jeu(g1)
```

    X|O|
     | |
    X| |


    [[1, 2, 0], [0, 0, 0], [1, 0, 0]]
    Entrez les coordonnées de votre coup :  0 0


    Coup invalide, tricher c'est mal !
    X|O|
     | |
    X| |


    [[1, 2, 0], [0, 0, 0], [1, 0, 0]]
    Entrez les coordonnées de votre coup :  1 0


    X|O|
    O| |
    X| |X


    [[1, 2, 0], [2, 0, 0], [1, 0, 1]]
    Entrez les coordonnées de votre coup :  1 0


    Coup invalide, tricher c'est mal !
    X|O|
    O| |
    X| |X


    [[1, 2, 0], [2, 0, 0], [1, 0, 1]]
    Entrez les coordonnées de votre coup :  1 1


    Le joueur 1 a gagné !
    X|O|
    O|O|
    X|X|X


**Question** : Implémentez la fonction jeu2() vous permettant de jouer à 2


```python
def jeu2():
    g = L([[0, 0, 0], [0, 0, 0], [0, 0, 0]])
    while gagnant(g) == 0:
        afficher(g)
        i, j = map(int, input(f"{g}\nJoueur {joueur(g)}, entrez les coordonnées de votre coup : ").split())
        if g[i][j]==0:
            g[i][j] = joueur(g)
        else:
            print("Coup invalide, tricher c'est mal !")
    print("Le joueur", gagnant(g), "a gagné !")
    afficher(g)

jeu2()
```

     | |
     | |
     | |


    [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
    Joueur 1, entrez les coordonnées de votre coup :  0 0


    X| |
     | |
     | |


    [[1, 0, 0], [0, 0, 0], [0, 0, 0]]
    Joueur 2, entrez les coordonnées de votre coup :  1 1


    X| |
     |O|
     | |


    [[1, 0, 0], [0, 2, 0], [0, 0, 0]]
    Joueur 1, entrez les coordonnées de votre coup :  0 1


    X|X|
     |O|
     | |


    [[1, 1, 0], [0, 2, 0], [0, 0, 0]]
    Joueur 2, entrez les coordonnées de votre coup :  1 0


    X|X|
    O|O|
     | |


    [[1, 1, 0], [2, 2, 0], [0, 0, 0]]
    Joueur 1, entrez les coordonnées de votre coup :  0 2


    Le joueur 1 a gagné !
    X|X|X
    O|O|
     | |



```python

```
