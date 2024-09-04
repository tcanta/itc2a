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

# TP : Révisions de 1ère année

Pour programmer, vous pouvez soit utiliser Pyzo, soit [Basthon](https://notebook.basthon.fr?href=tcanta.github.io/eleve/tp_revisions-eleve.ipynb).

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

## Bases de Python

+++

:::{admonition} Exercice 1
:class: note

Calculer $u_{10}$, où $u_n$ est définie par $u_0 = 42$ et $u_{n+1} = \sqrt{u_n} + 3u_n$.  
Il faut trouver 2787204.895558157.

:::


**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]
u = 42
for i in range(10):
    u = u**0.5 + 3*u
u

```

---

:::{admonition} Exercice 2
:class: note

Écrire une fonction `appartient` telle que `appartient(x, L)` renvoie `True` si `x` appartient à la liste `L`, `False` sinon.

:::


**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

def appartient(x, L):
    for e in L:
        if e == x:
            return True
    return False
```

```{code-cell} ipython3
print(appartient(3, [1, 5, 3, 4, 1]))
print(appartient(3, [1, 5, 4, 2]))
```

---

:::{admonition} Exercice 3
:class: note

Écrire une fonction pour déterminer si une liste est triée par ordre croissant.

:::


**Solution**

```{code-cell} ipython3
:tags: ["hide-cell"]

def croissant(L):
    for i in range(len(L)-1):
        if L[i] > L[i+1]:
            return False
    return True
```

```{code-cell} ipython3
print(croissant([1, 2, 3, 4, 5]))
print(croissant([1, 2, 3, 5, 4]))
```
---
## Dichotomie

La méthode par dichotomie consiste à divise à chaque étape la taille du problème par 2.  
L'exemple le plus classique est la recherche par dichotomie dans une liste **triée** : on souhaite savoir si un élément $e$ appartient à une liste **triée** `L`.  

+++

Considérons par exemple `L` = $[-2, 1, 2, 4, 6, 7, 8, 9, 11, 12, 14, 15, 18, 22, 54]$ et $e$ = $14$.  

Au lieu de commencer par regarder le 1er élément de `L`, on va regarder l'élément du milieu (ici $9$):

$$
    [-2, 1, 2, 4, 6, 7, 8, \underline{\mathbf{9}}, 11, 12, 14, 15, 18, 22, 54]
$$

Comme $9 < 14$ et que la liste est triée par ordre croissant, on en déduit que $e$, s'il est dans `L`, est forcément dans la partie droite :

$$
    [-2, 1, 2, 4, 6, 7, 8, {9}, \boxed{11, 12, 14, {15}, 18, 22, 54}]
$$

On se restreint donc par la suite à la partie de `L` qui est encadrée. On compare $e$ au milieu de cette partie, c'est-à-dire $15$ :

$$
    [-2, 1, 2, 4, 6, 7, 8, {9}, \boxed{11, 12, 14, \underline{\textbf{15}}, 18, 22, 54}]
$$

Comme $e < 15$, on peut cette fois se restreindre à la partie gauche. On cherche donc maintenant $e$ dans la zone suivante :

$$
    [-2, 1, 2, 4, 6, 7, 8, {9}, \boxed{11, {12}, 14}, {15}, 18, 22, 54]
$$

On compare encore une fois $e$ au milieu :

$$
    [-2, 1, 2, 4, 6, 7, 8, {9}, \boxed{11, \underline{\textbf{12}}, 14}, {15}, 18, 22, 54]
$$

Comme $e > 12$, on regarde à droite :

$$
    [-2, 1, 2, 4, 6, 7, 8, {9}, 11, {12}, \boxed{\underline{\textbf{14}}}, {15}, 18, 22, 54]
$$

On a trouvé $e$ !

+++

:::{admonition} Exercice 4
:class: note

Compléter le code suivant permettant de rechercher un élément dans une liste triée, par dichotomie.
```python
def dichotomie(e, L):
    i, j = 0, len(L) - 1  # i et j sont les indices de L entre lesquels on cherche e
    while ...: # tant qu'il reste au moins 1 élément entre les indices i et j
        m = ... # milieu de i et j
        if e < L[m]:
            ... # regarder dans la partie gauche
        elif e > L[m]:
            ... # regarder dans la partie droite
        else:
            ... # on a trouvé e
    ... # e n'a pas été trouvé
```
Puis tester votre fonction avec le jeu de tests suivant (`assert` déclenche une erreur si son argument est `False`) :  
```python
L1, L2, L3 = [0, 2], [0, 2, 5], [-2, 1, 2, 4, 6, 7, 8, 9, 11, 12, 14, 15, 18, 22, 54]
assert (dichotomie(0, L1) and not dichotomie(1, L1))
assert (dichotomie(5, L2) and not dichotomie(7, L2))
assert (dichotomie(14, L3) and not dichotomie(-4, L3))
```
:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def dichotomie(e, L):
    i, j = 0, len(L) - 1
    while i <= j:
        m = (i+j)//2
        if e < L[m]:
            j = m - 1
        elif e > L[m]:
            i = m + 1
        else:
            return True
    return False
```
```{code-cell} ipython3
L1, L2, L3 = [0, 2], [0, 2, 5], [-2, 1, 2, 4, 6, 7, 8, 9, 11, 12, 14, 15, 18, 22, 54]
assert (dichotomie(0, L1) and not dichotomie(1, L1))
assert (dichotomie(5, L2) and not dichotomie(7, L2))
assert (dichotomie(14, L3) and not dichotomie(-4, L3))
```

Le comportement en cas d'erreur est le suivant :
```python
assert (dichotomie(42, L1))
```
```python
---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
Cell In[8], line 1
----> 1 assert (dichotomie(42, L1))

AssertionError:
```

## Matrices

+++
:::{admonition} Exercice 5
:class: note

Définir une fonction `make_matrix` telle que `make_matrix(n, p)` renvoie une matrice $n\times p$ remplie de $0$, sous forme de liste de listes.

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

# On propose plusieurs possibilités

def make_matrix(n, p):
    return [[0]*p for _ in range(n)]

def make_matrix2(n, p):
    M = []
    for i in range(n):
        M.append([0]*p)
    return M

def make_matrix3(n, p):
    M = []
    for i in range(n):
        L = []
        for j in range(p):
            L.append(0)
        M.append(L)
    return M
```

```{code-cell} ipython3
make_matrix(3, 4)
```

---

:::{admonition} Exercice 6
:class: note

Écrire une fonction `transposee(M)` qui renvoie la transposée de la matrice `M`.

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def transposee(M):
    n = len(M)
    p = len(M[0])
    Mt = make_matrix(p, n)
    for i in range(n):
        for j in range(p):
            Mt[j][i] = M[i][j]
    return Mt
```

```{code-cell} ipython3
transposee([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
```

---

:::{admonition} Exercice 7
:class: note

Écrire une fonction `symetrique(M)` qui renvoie `True` si la matrice `M` est symétrique, `False` sinon.

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def symetrique(M):
    for i in range(len(M)):
        for j in range(i):
            if M[i][j] != M[j][i]:
                return False
    return True
```

```{code-cell} ipython3
print(symetrique([[1, 2, 3], [2, 4, 5], [3, 5, 6]])) # True
print(symetrique([[1, 2, 3], [2, 4, 7], [3, 5, 6]])) # False
```

## Graphes

[Si besoin, vous pouvez revoir ce cours (Graphes : Représentations)](https://cpge-itc.github.io/itc1/5_graph/2_representation/2_representation).

### Représentation par matrice d'adjacence

+++

:::{admonition} Exercice 8
:class: note

Définir dans une variable `G` la matrice d'adjacence du graphe suivant (on pourra éventuellement utiliser les fonctions précédentes) :
<center><img src=https://github.com/cpge-itc/itc1/raw/4be1ee8d9679ffae521c506ad54acb9e6099c614/files/5_graph/tp/tp2/g.png width=200></center>

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

G = make_matrix(7, 7)
# façon rapide de remplir la matrice
for i, j in [(0, 6), (1, 2), (1, 3), (1, 4), (2, 4), (2, 5), (3, 4), (4, 5)]:
    G[i][j] = G[j][i] = 1
G
```

---

:::{admonition} Exercice 9
:class: note

Définir une fonction `voisins` telle que `voisins(G, v)` renvoie la liste des voisins du sommet `v`.  
Vérifier que les voisins du sommet $2$ dans le graphe ci-dessus sont les sommets $1$, $4$, $5$.

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def voisins(G, v):
    L = []
    for i in range(len(G)):
        if G[v][i] == 1:
            L.append(i)
    return L
```

```{code-cell} ipython3
voisins(G, 2)
```

---

:::{admonition} Exercice 10
:class: note

En déduire une fonction `deg` telle que `deg(G, v)` renvoie le degré du sommet `v`.

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def deg(G, v):
    return len(voisins(G, v))
```

```{code-cell} ipython3
deg(G, 2)
```

---

:::{admonition} Exercice 11
:class: note

Écrire une fonction `n_aretes` pour calculer le nombre d'arêtes d'un graphe donné par matrice d'adjacence. Tester avec le graphe `G` précédent. On pourra soit réutiliser `deg`, soit deux boucles `for` pour parcourir les éléments de la matrice.

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def n_aretes(G):
    s = 0
    for i in range(len(G)):
        s += deg(G, i)
    return s//2
```

```{code-cell} ipython3
n_aretes(G)
```

### Représentation par liste d'adjacence

+++

:::{admonition} Exercice 12
:class: note

Écrire une fonction `mat_to_list` telle que `mat_to_list(G)` renvoie la liste d'adjacence du graphe `G` donné par matrice d'adjacence. Calculer la représentation par liste d'adjacence `G_list` du graphe `G` précédent.

<center><img src=https://github.com/cpge-itc/itc1/raw/4be1ee8d9679ffae521c506ad54acb9e6099c614/files/5_graph/tp/tp2/g.png width=200></center>

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def mat_to_list(G):
    L = []
    for i in range(len(G)):
        L.append(voisins(G, i))
    return L
```

```{code-cell} ipython3
G_list = mat_to_list(G)
G_list
```

### Parcours en profondeur

[Revoir ce cours si besoin](https://cpge-itc.github.io/itc1/5_graph/3_traversal/dfs/dfs)

+++

:::{admonition} Exercice 13
:class: note

Écrire une fonction `dfs(G, s)` renvoyant la liste des sommets de `G` (défini par liste d'adjacence) suivant un parcours en profondeur depuis le sommet `s`. Vérifier sur le graphe `G_list` précédent.  
On pourra compléter le code ci-dessous. Ne regarder le cours que si cela est vraiment nécessaire.

```python
def dfs(G, s):
    # définir une liste de booléens pour savoir si un sommet a été visité
    def aux(u): # fonction récursive
        # si u n'a pas été visité :
            # afficher u
            # marquer u comme visité
            # pour chaque voisin v de u
                # appeler aux(v)
    aux(s)
```
:::


**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

#Simple affichage des sommets
def dfs(G, s):
    visited = [False]*len(G)
    def aux(u):
        if not visited[u]:
            print(u)
            visited[u] = True
            for v in G[u]:
                aux(v)
    aux(s)

# Avec un return de la liste demandée :
def dfs(G, s):
    result = []
    visited = [False] * len(G)
    def aux(u, result): # fonction récursive
        if not visited[u]:
            result.append(u)
            visited[u]=True
            for x in G[u]:
                aux(x, result)
    aux(s,result)
    return(result)
```

```{code-cell} ipython3
dfs(G_list, 0)
```

```{code-cell} ipython3
dfs(G_list, 2)
```

---

:::{admonition} Exercice 14
:class: note

En utilisant un parcours en profondeur, écrire une fonction `connexe(G)` qui renvoie `True` si le graphe `G` est connexe, `False` sinon.  
Vérifier sur `G_list` (non connexe) et sur un graphe connexe de votre choix.

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

# deux solutions

def connexe(G):
    visited = [False]*len(G)
    def aux(u):
        if not visited[u]:
            visited[u] = True
            for v in G[u]:
                aux(v)
    aux(0)
    return all(visited) # vérifie que tous les éléments de visited sont True

def connexe(G):
    visited = [False]*len(G)
    def aux(u):
        if not visited[u]:
            visited[u] = True
            for v in G[u]:
                aux(v)
    aux(0)
    for v in range(len(G)):
        if not visited[v]:
            return False
    return True
```

```{code-cell} ipython3
connexe(G_list)
```

### Parcours en largeur

[Revoir ce cours si besoin](https://cpge-itc.github.io/itc1/5_graph/3_traversal/bfs/bfs)

+++

On rappelle l'utilisation d'une file en Python, avec la classe `deque` :

```{code-cell} ipython3
from collections import deque

q = deque() # file vide
q.appendleft(4) # ajoute 4 au début
q.appendleft(7)
q.pop() # supprime et renvoie le dernier élément (4)
q.appendleft(-5)
q.pop() # supprime et renvoie le dernier élément (7)
```

:::{admonition} Exercice 15
:class: note

Écrire une fonction `bfs(G, s)` affichant les sommets du graphe `G` lors d'un parcours en largeur depuis le sommet `s`. Vérifier sur le graphe `G_list` précédent.  

<center><img src=https://github.com/cpge-itc/itc1/raw/4be1ee8d9679ffae521c506ad54acb9e6099c614/files/5_graph/tp/tp2/g.png width=200></center>

```python
from collections import deque

def bfs(G, s):
    # définir une file q contenant s
    # définir une liste visited contenant n False, où n est le nombre de sommets
    # tant que q est non vide
        # extraire le dernier élément de q dans une variable u
        # si u n'a pas été visité:
            # afficher u
            # mettre True dans visited[u]
            # pour chaque voisin v de u
                # ajouter v au début de q
```

:::


**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

from collections import deque

def bfs(G, s):
    q = deque()
    q.appendleft(s)
    visited = [False]*len(G)
    while q:
        u = q.pop()
        if not visited[u]:
            print(u)
            visited[u] = True
            for v in G[u]:
                q.appendleft(v)
```

```{code-cell} ipython3
bfs(G_list, 2)
```


:::{admonition} Exercice 16
:class: note

Écrire une fonction `distances(G, s)` qui renvoie une liste `dist` telle que `dist[v]` soit la distance (en nombre d'arêtes) du sommet `s` au sommet `v`. Vérifier sur le graphe `G_list` précédent.  

<center><img src=https://github.com/cpge-itc/itc1/raw/4be1ee8d9679ffae521c506ad54acb9e6099c614/files/5_graph/tp/tp2/g.png width=200></center>

:::

**Solution** :
```{code-cell} ipython3
:tags: ["hide-cell"]

def distances(G, s):
    q = deque()
    q.appendleft(s)
    visited = [False]*len(G)
    dist = [float("inf")]*len(G)
    dist[s] = 0
    while q:
        u = q.pop()
        visited[u] = True
        for v in G[u]:
            if not visited[v]:
                q.appendleft(v)
                dist[v] = dist[u] + 1
    return dist
```

```{code-cell} ipython3
distances(G_list, 2)
```
