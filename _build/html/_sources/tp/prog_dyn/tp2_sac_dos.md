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
# TP 2 - Knapsack

Pour programmer, vous pouvez soit utiliser Pyzo, soit [Basthon](https://notebook.basthon.fr/?from=https://raw.githubusercontent.com/tcanta/itc2a/master/eleve/tp2_sac_dos-eleve.ipynb).

Commandes Basthon :

- Ctrl + Entrée : exécuter la cellule
- Shift + Entrée : exécuter la cellule puis passer à la suivante
- Entrée : passer en mode édition
- Échap : sortir du mode édition

Les commandes suivantes sont valables uniquement hors du mode édition :

- A : créer une nouvelle cellule (en haut)
- B : créer une nouvelle cellule (en bas)
- D D : supprimer la cellule

+++
## Problème du sac à dos

<center><img src=https://upload.wikimedia.org/wikipedia/commons/thumb/f/fd/Knapsack.svg/486px-Knapsack.svg.png width=250></center>

On rappelle le problème du sac à dos :
- **Entrée** : une capacité $c$ et des objets de poids $w_1, ..., w_n$ et valeurs $v_1$, ..., $v_n$.
- **Sortie** : la valeur maximum que l'on peut mettre dans un sac de capacité $c$.  

$c$ est le poids total maximum que l'on peut peut mettre dans le sac

L'objectif du TP est de comparer l'algorithme de programmation dynamique vu en cours à des algorithmes gloutons.

### Algorithmes gloutons

Un algorithme glouton consiste à ajouter des objets un par un au sac, en choisissant à chaque étape l'objet qui a l'air le plus intéressant, si son poids n'excède pas la capacité restante du sac.  
Suivant l'ordre dans lequel on choisit les objets, on obtient des algorithmes gloutons différents.

:::{admonition} Question
:class: note
Écrire une fonction `glouton(c, w, v)` qui renvoie la valeur totale des objets choisis par l'algorithme glouton, en considérant les objets dans l'ordre donné par `w` et `v` (on regarde d'abord l'objet de poids `w[0]` et valeur `v[0]`, puis l'objet de poids `w[1]` et valeur `v[1]`...).  
Tester avec l'exemple ci-dessous. Le résultat est-il optimal ?
:::

**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]
def glouton(c, w, v):
    """Renvoie la valeur maximum qu'on peut obtenir avec les objets
    c: capacité du sac
    w: poids des objets
    v: valeur des objets
    """
    poids = 0
    valeur = 0
    for i in range(len(w)):
        if poids + w[i] <= c:
            poids += w[i]
            valeur += v[i]
    return valeur
```
```{code-cell} ipython3
glouton(10, [5, 3, 6], [4, 4, 6])
```
### Tri des objets

:::{admonition} Question
:class: note
Écrire une fonction `combine(L1, L2)` qui renvoie la liste des couples `(L1[i], L2[i])`. On suppose que `L1` et `L2` ont la même longueur.
:::

**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]
def combine(L1, L2):
    L = []
    for i in range(len(L1)):
        L.append((L1[i], L2[i]))
    return L
```

```{code-cell} ipython3
combine([1, 2, 3], [4, 5, 6])
```

:::{admonition} Question
:class: note
Écrire une fonction `split(L)` telle que si `L` est une liste de couples, `split(L)` renvoie deux listes `L1` et `L2` où `L1` contient les premiers éléments des couples de `L` et `L2` les seconds éléments des couples de `L`.
:::
**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]
def split(L):
    L1 = []
    L2 = []
    for i in range(len(L)):
        L1.append(L[i][0])
        L2.append(L[i][1])
    return L1, L2
```
```{code-cell} ipython3
split([(1, 4), (2, 5), (3, 6)])
```

Si `L` est une liste, `L.sort()` trie `L` par ordre croissant (`L.sort(reverse=True)` pour trier par ordre décroissant). Si `L` contient des couples, la liste est triée suivant le premier élément de chaque couple (ordre lexicographique).  
Exemple :
```{code-cell} ipython3
L = [(1, 4), (7, 5), (3, 6)]
L.sort()
L # trié suivant le 1er élément de chaque couple
```
:::{admonition} Question
:class: note
Écrire une fonction `tri_poids(w, v)` qui renvoie les listes `w2` et `v2` obtenues à partir de `w` et `v` en triant les poids par ordre croissant. On pourra utiliser `L.sort`, `combine` et `split`.
:::

**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]
def tri_poids(w, v):
    L = combine(w, v)
    L.sort()
    return split(L)
```
```{code-cell} ipython3
tri_poids([5, 3, 6], [42, 0, 2])
```
---

### Stratégies gloutonnes

:::{admonition} Question
:class: note
En déduire une fonction `glouton_poids(c, w, v)` qui renvoie la valeur totale des objets choisis par l'algorithme glouton, en considérant les objets dans l'ordre de poids croissant. On pourra réutiliser `glouton`.  
Est-ce que cet algorithme est toujours optimal ?
:::
**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

def glouton_poids(c, w, v):
    w, v = tri_poids(w, v)
    return glouton(c, w, v)
```
```{code-cell} ipython3
glouton_poids(10, [5, 3, 6], [4, 4, 10])
```
:::{admonition} Question
:class: note
Écrire de même des fonctions `tri_valeur(w, v)` et `glouton_valeur(c, w, v)` qui renvoie la valeur totale des objets choisis par l'algorithme glouton, en considérant les objets dans l'ordre de valeur décroissante (en utilisant `L.sort(reverse=True)`). Est-ce que cet algorithme est toujours optimal ?
:::
**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

def tri_valeur(w, v):
    L = combine(v, w)
    L.sort(reverse=True)
    L1, L2 = split(L)
    return L2, L1

def glouton_valeur(c, w, v):
    w, v = tri_valeur(w, v)
    return glouton(c, w, v)
```
```{code-cell} ipython3
glouton_valeur(10, [5, 4, 7], [4, 4, 6])
```
:::{admonition} Question
:class: note
De même, écrire une fonction `glouton_ratio(c, w, v)` qui renvoie la valeur totale des objets choisis par l'algorithme glouton, en considérant les objets dans l'ordre de ratio valeur/poids décroissant. On pourra utiliser deux fois `combine`.
:::
**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

def tri_ratio(v, w):
    L = combine(v, w)
    L = combine([v[i]/w[i] for i in range(len(v))], L)

    L.sort(reverse=True)
    return split(split(L)[1])

def glouton_ratio(c, w, v):
    v, w = tri_ratio(v, w)
    return glouton(c, w, v)

```
```{code-cell} ipython3
glouton_ratio(10, [5, 4, 7], [4, 4, 6])
```

### Programmation dynamique

On rappelle le problème du sac à dos :
- **Entrée** : une capacité $c$ et des objets de poids $w_1, ..., w_n$ et valeurs $v_1$, ..., $v_n$.
- **Sortie** : la valeur maximum que l'on peut mettre dans un sac de capacité $c$.

Soit $dp[i][j]$ la valeur maximum que l'on peut mettre dans un sac de capacité $i$, en ne considérant que les $j$ premiers objets. On suppose que les poids sont strictement positifs.

:::{admonition} Question
:class: note
Que vaut $dp[i][0]$ ?
:::

:::{dropdown} Solution
:animate: fade-in
$dp[i][0] = 0$ : on ne peut pas mettre d'objet dans un sac quand on en a pas...
:::

:::{admonition} Question
:class: note
Exprimer $dp[i][j]$ en fonction de $dp[i][j-1]$ dans le cas où $w_j > i$.
:::

:::{dropdown} Solution
:animate: fade-in
$dp[i][j] = dp[i][j-1]$ : on ne peut pas mettre l'objet $j$ dans le sac de capacité $i$.
:::

:::{admonition} Question
:class: note
Supposons $w_j \leq i$. Donner une formule de récurrence sur $dp[i][j]$, en distinguant le cas où l'objet $j$ est choisi et le cas où il ne l'est pas.
:::

:::{dropdown} Solution
:animate: fade-in
$$dp[i][j] = \max(\underbrace{dp[i][j - 1]}_{\text{sans prendre } o_j}, \underbrace{dp[i - w_j][j - 1] + v_j}_{\substack{\text{en prenant } o_j}, \text{si }i - w_j \geq 0})$$
:::

:::{admonition} Question
:class: note
En déduire une fonction `prog_dyn(c, w, v)` qui renvoie la valeur maximum que l'on peut mettre dans un sac de capacité $c$, en ne considérant que les $j$ premiers objets, en remplissant une matrice `dp` de taille $(c+1) \times (n+1)$.
:::
**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

def prog_dyn(c, w, v):
    n = len(w)
    dp = [[0 for j in range(c+1)] for i in range(n+1)]
    for i in range(1, n+1):
        for j in range(1, c+1):
            if j < w[i-1]:
                dp[i][j] = dp[i-1][j]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i-1][j-w[i-1]] + v[i-1])
    return dp[n][c]
```
```{code-cell} ipython3
prog_dyn(10, [5, 4, 7], [4, 4, 6])
```

---
### Comparaison

:::{admonition} Question
:class: note
Écrire une fonction `genere_instance()` qui renvoie un triplet $(c, w, v)$, où $c$ est un entier aléatoire entre 1 et 1000 et $w$, $v$ sont des listes de 100 entiers aléatoires entre 1 et 100.  
On importera `random` pour utiliser `random.randint(a, b)` qui génère un entier aléatoire entre $a$ et $b$ inclus.
:::
**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

import random

def genere_instance():
    c = random.randint(1, 1000)
    w = [random.randint(1, 100) for i in range(100)]
    v = [random.randint(1, 100) for i in range(100)]
    return c, w, v
```

:::{admonition} Question
:class: note
Afficher, pour chaque stratégie gloutonne (ordre de poids, ordre de valeur, ordre de ratio), l'erreur commise par rapport à la solution optimale, en moyennant sur 100 instances générées par `genere_instance()`.  
Quelle stratégie gloutonne est la plus efficace ?
:::

**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

gp, gv, gr = 0, 0, 0
for i in range(100):
    c, w, v = genere_instance()
    sol = prog_dyn(c, w, v)
    gp += glouton_poids(c, w, v)/sol
    gv += glouton_valeur(c, w, v)/sol
    gr += glouton_ratio(c, w, v)/sol
print(f"Glouton poids : {gp/100}")
print(f"Glouton valeur : {gv/100}")
print(f"Glouton ratio : {gr/100}")
```

:::{admonition} Question
:class: note
Comparer le temps total d'exécution de la stratégie gloutonne par ratio et de la programmation dynamique, sur 100 instances générées par `genere_instance()`. On pourra importer `time` et utiliser `time.time()` pour obtenir le temps actuel en secondes.
:::

**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

import time

t1, t2 = 0, 0
for i in range(100):
    c, w, v = genere_instance()
    t = time.time()
    glouton_poids(c, w, v)
    t1 += time.time() - t
    t = time.time()
    prog_dyn(c, w, v)
    t2 += time.time() - t
print(f"Glouton poids : {t1} s")
print(f"Programmation dynamique : {t2} s")
```
---
### Obtenir la liste des objets choisis

:::{admonition} Question
:class: note
Réécrire la fonction `prog_dyn(c, w, v)` pour qu'elle renvoie la liste des objets choisis. Pour cela, on peut construire la matrice `dp` et remarquer que :  
- si $dp[i][j] = dp[i][j-1]$, alors l'objet $j$ n'est pas choisi ;
- si $dp[i][j] = dp[i - w_j][j - 1] + v_j$, alors l'objet $j$ est choisi.  
On peut donc construire la liste des objets choisis en remontant la matrice `dp` à partir de la case $(c, n)$.
:::
**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]
def prog_dyn(c, w, v):
    n = len(w)
    dp = [[0 for j in range(c+1)] for i in range(n+1)]
    for i in range(1, n+1):
        for j in range(1, c+1):
            if j < w[i-1]:
                dp[i][j] = dp[i-1][j]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i-1][j-w[i-1]] + v[i-1])

    # reconstruction de la solution
    i, j = n, c
    sol = []
    while i > 0 and j > 0:
        if dp[i][j] == dp[i-1][j]:
            i -= 1
        else:
            sol.append(i-1)
            j -= w[i-1]
            i -= 1
    return sol
```
```{code-cell} ipython3
prog_dyn(10, [5, 4, 7], [4, 4, 6])
# la solution optimale consiste à choisir les objets 1 et 0
```
---

## Plus long chemin croissant dans une matrice


Etant donnée une matrice d'entiers de taille `m` x `n`, quelle est la longueur du plus long chemin croissant dans cette matrice ?

Pour chaque élément dans la matrice, on peut bouger dans les quatre directions (haut, bas, droite et gauche) mais pas en diagonale ou en dehors de la matrice.

Exemple 1 :

<center><img src=https://raw.githubusercontent.com/tcanta/itc2a/master/img/grid1.jpg width=250></center>

Exemple 2 :

<center><img src=https://raw.githubusercontent.com/tcanta/itc2a/master/img/grid2.jpg width=260></center>

:::{admonition} Question
:class: note
Proposez une fonction renvoyant la longueur du plus long chemin croissant dans une matrice. Votre solution devra utiliser de la programmation dynamique.
:::

:::{dropdown} Indice 1
:animate: fade-in
Posez les cas limites qui vont vous embeter et pensez à votre structure de données.
:::

:::{dropdown} Indice 2
:animate: fade-in
Utilisez une fonction auxiliaire qui vous résoud un sous-problème...
:::

:::{dropdown} Indice 3
:animate: fade-in
... c'est à dire une fonction qui résoud le plus grand chemin croissant en partant d'une cellule donnée dans la matrice.
:::

**Solution**

Vous êtes vraiment sur de vouloir abandonner si proche du but ?

```{code-cell} ipython3
:tags: ["hide-cell"]
m1 = [[9,9,4],[6,6,8],[2,1,1]]
m2 = [[3,4,5],[3,2,6],[2,2,1]]


def lip(mat, i, j, d):
    current = mat[i][j]
    bn = [0,0,0,0] #better neighbours
    left = right = top = bottom = True

    if i <= 0:
        top = False
    if i >= len(mat)-1:
        bottom = False
    if j <= 0:
        left = False
    if j >= len(mat[0])-1:
        right = False

    if top:
        if mat[i-1][j]>current:
            if d[i-1][j] == 0:
                d[i-1][j] = lip(mat,i-1,j, d)
            bn[0] = d[i-1][j]
    if bottom:
        if mat[i+1][j]>current:
            if d[i+1][j] == 0:
                d[i+1][j] = lip(mat,i+1,j, d)
            bn[1] = d[i+1][j]
    if left:
        if mat[i][j-1]>current:
            if d[i][j-1] == 0:
                d[i][j-1] = lip(mat,i,j-1, d)
            bn[2] = d[i][j-1]
    if right:
        if mat[i][j+1]>current:
            if d[i][j+1] == 0:
                d[i][j+1] = lip(mat,i,j+1, d)
            bn[3] = d[i][j+1]

    if d[i][j] == 0:
        d[i][j] = 1 + max(bn[0] ,bn[1], bn[2], bn[3])
    return 1 + max(bn[0] ,bn[1], bn[2], bn[3])

def longestIncreasingPath(mat):
    d = [[0]*len(mat[0]) for _ in range(len(mat))]
    max_coords=(0,0)
    maxi = 0
    for i in range(len(mat)):
        for j in range(len(mat[0])):
            if lip(mat, i, j, d) > maxi:
                max_coords = (i,j)
                maxi = lip(mat, i, j, d)
    return maxi, max_coords, d
```
```{code-cell} ipython3
longestIncreasingPath(m1)
```
```{code-cell} ipython3
longestIncreasingPath(m2)
```
:::
