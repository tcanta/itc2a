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
# TP : Classification de voitures

Pour programmer, vous pouvez soit utiliser Pyzo, soit [Basthon](https://notebook.basthon.fr/?from=https://raw.githubusercontent.com/tcanta/itc2a/master/eleve/tp_voitures-eleves.ipynb).

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

## Chargement des données
Dans ce TP, nous voulons classifier des voitures, selon leur type (sportive, citadine, familiale...). Commençons par charger les données dans Basthon :

1. Télécharger <a href="https://raw.githubusercontent.com/tcanta/itc2a/master/tp/data_science/voitures.csv" download>les données (clic droit ici puis enregistrer la cible du lien sous)</a>.  
2. Dans Basthon, cliquer sur Fichier puis Ouvrir et sélectionner le fichier téléchargé.
3. Exécuter le code ci-dessous, en modifiant titanic.csv si vous avez utilisé un autre nom de fichier.



```{code-cell} ipython3
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

voitures = pd.read_csv("voitures.csv")
voitures

```

Pour éviter d'utiliser des dataframes pandas, nous allons utiliser des listes `attributs` et `noms` tels que `attributs[i]` contienne les caractéristiques (cyindrée, chevaux, longueur...) et `noms[i]` le nom de la voiture $i$ :


```{code-cell} ipython3
noms = voitures.iloc[:, :2].to_numpy().tolist()
noms[0] # affiche le nom de la première voiture
```

```{code-cell} ipython3
attributs = voitures.iloc[:, 2:].to_numpy().tolist()
attributs[0] # affiche les valeurs des attributs de la première voiture
```
Pour obtenir la signification des attributs :

```{code-cell} ipython3
attributs_noms = voitures.columns[2:].to_numpy().tolist()
attributs_noms
```
## Standardisation des données

:::{admonition} Question
:class: note
Écrire une fonction `moyenne(j)` qui renvoie la moyenne de l'attribut `j` (c'est-à-dire la moyenne de `attributs[i][j]`, pour tous les `i` possibles).
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def moyenne(j):
    return sum([x[j] for x in attributs])/len(attributs)
```

```{code-cell} ipython3
moyenne(7) # nombre moyen de secondes pour aller de 0 à 100 km/h
```

:::{admonition} Question
:class: note
Écrire une fonction `ecart_type(j)` qui renvoie l'écart-type de l'attribut `j` (c'est-à-dire $\sigma = \sqrt{\sum_i \frac{(x_i - \mu)^2}{n}}$ où les $x_i$ sont les valeurs pour l'attribut `j` et $\mu$ est la moyenne de l'attribut `j`).
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def ecart_type(j):
    m = moyenne(j)
    return (sum([(x[j] - m)**2 for x in attributs])/len(attributs))**0.5
```
```{code-cell} ipython3
ecart_type(0)
```
Si des données $x_1, ..., x_n$ ont une moyenne $\mu$ et un écart-type $\sigma$, on peut standardiser ces données, c'est-à-dire se ramener à une moyenne à $0$ et un écart-type à $1$, en remplaçant chaque $x_i$ par :

$$\frac{x_i - \mu}{\sigma}$$


:::{admonition} Question
:class: note
Définir une matrice `X` qui contient les caractéristiques standardisées des voitures.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
X = []
for i in range(len(attributs)):
    X.append([(attributs[i][j] - moyenne(j))/ecart_type(j) for j in range(len(attributs[i]))])
```
```{code-cell} ipython3
X[0]
```

:::{admonition} Question
:class: note
On aura aussi besoin de réaliser l'opération inverse de la précédente. Écrire une fonction `inverse_standardisation(y)` qui, pour une voiture `y` (c'est-à-dire la liste de ses attributs), renvoie une liste `x` telle que $x_i = y_i \sigma_i + \mu_i$, où $\mu_i$ et $\sigma_i$ sont la moyenne et l'écart-type de l'attribut $i$.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def inverse_standardisation(y):
    return [y[i]*ecart_type(i) + moyenne(i) for i in range(len(y))]
```
```{code-cell} ipython3
inverse_standardisation(X[0]) # doit être égal à attributs[0]
```

## Algorithme des k-moyennes

:::{admonition} Question
:class: note
Écrire une fonction `d(x, y)` qui calcule la distance euclidienne entre deux attributs de voitures.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def d(x, y):
    s = 0
    for i in range(len(x)):
        s += (x[i] - y[i])**2
    return s**0.5
```

```{code-cell} ipython3
d(X[0], X[1])
```

:::{admonition} Question
:class: note
Écrire une fonction `centre(indices)` qui renvoie le centre des `X[i]` pour `i` dans la liste `indices`.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def centre(indices):
    c = [0]*len(X[0])
    for i in indices:
        for j in range(len(c)):
            c[j] += X[i][j]
    if len(indices) != 0:
        for j in range(len(c)):
            c[j] /= len(indices)
    return c
```
```{code-cell} ipython3
centre([0, 2, 7]) # centre des voitures 0, 2 et 7
```
Dans la suite, on utilisera une liste `classes` telle que `classes[i]` est la liste des indices `j` tels que `X|j]` est dans la classe `i`.  
Par exemple, si `classes = [[0, 8, 11], [2, 7]]` alors la classe numéro $0$ contient les voitures $0$, $8$, $11$ (dont les valeurs sont `X[0]`, `X[8]` et `X[11]`).

:::{admonition} Question
:class: note
Écrire une fonction `calculer_centres(classes)` qui renvoie une liste contenant les centres de chaque classe. Il faut donc que `centres[i]` soit le centre de la classe `i`.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def calculer_centres(classes):
    centres = []
    for c in classes:
        centres.append(centre(c))
    return centres
```
```{code-cell} ipython3
calculer_centres([[0, 8, 11], [2, 7]])
```
On initialisera l'algorithme des k-moyennes avec des centres aléatoires en utilisant la fonction suivante :

```{code-cell} ipython3
def centres_aléatoires(k):
    np.random.seed(1)
    return np.random.rand(k, len(X[0])).tolist()

centres_aléatoires(2) # exemple de 2 centres aléatoires
```

:::{admonition} Question
:class: note
Écrire une fonction `plus_proche(i, centres)` qui renvoie l'indice du centre le plus proche de la voiture `X[i]`. Il faut donc renvoyer `j` tel que `d(X[i], centres[j])` est minimum.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def plus_proche(i, centres):
    jmin = 0
    for j in range(1, len(centres)):
        d1 = d(X[i], centres[j])
        if d1 < d(X[i], centres[jmin]):
            jmin = j
    return jmin
```
```{code-cell} ipython3
plus_proche(0, centres_aléatoires(4))
```

:::{admonition} Question
:class: note
Écrire une fonction `calculer_classes(centres)` qui renvoie une liste `classes` de même taille que `centres` et contenant les classes obtenues en associant chaque `X[j]` au centre le plus proche.  
Ainsi, `classes[i]` doit contenir les indices `j` tel que le centre le plus proche de `X[j]` est `centres[i]`.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def calculer_classes(centres):
    classes = [[] for c in centres]
    for i in range(len(X)):
        classes[plus_proche(i, centres)].append(i)
    return classes
```
```{code-cell} ipython3
calculer_classes(centres_aléatoires(3))
```

:::{admonition} Question
:class: note
Écrire une fonction `k_moyennes(k)` qui renvoie les classes obtenues par l'algorithme des k-moyennes appliqué aux données `X`.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def k_moyennes(k):
    centres = centres_aléatoires(k)
    while True:
        classes = calculer_classes(centres)
        centres2 = calculer_centres(classes)
        if centres == centres2:
            return classes, centres
        centres = centres2
```
```{code-cell} ipython3
classes, centres = k_moyennes(3)
centres
```

:::{admonition} Question
:class: note
Écrire une fonction `inertie(classes, centres)` qui renvoie la somme des carrés des distances des voitures aux centres de leurs classes.
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
def inertie(classes, centres):
    s = 0
    for i in range(len(classes)):
        for j in classes[i]:
            s += d(X[j], centres[i])**2
    return s
```
## Choix de l'hyperparamètre k
### Elbow method
:::{admonition} Question
:class: note
Exécuter le code suivant pour afficher l'inertie en fonction du nombre de classes $k$. Quelle valeur de $k$ vous paraît-elle optimale ?
:::

```{code-cell} ipython3
kmax = 6
I = []
for k in range(1, kmax+1):
    classes,centres = k_moyennes(k)
    I.append(inertie(classes, centres))
plt.plot(range(1, kmax+1), I)
plt.xlabel("k")
plt.ylabel("inertie")
plt.show()
```

:::{admonition} Question
:class: note
On prend la valeur de $k$
 obtenue à la question précédente. Pour chaque classe, afficher les voitures de cette classe ainsi que le centre (en appliquant `inverse_standardisation` dessus).
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
classes, centres = k_moyennes(4)
for i in range(len(centres)):
    print("Centre", i, ":", inverse_standardisation(centres[i]))
    for j in classes[i]:
        print(" ", noms[j])
    print()
```

:::{admonition} Question
:class: note
Pour savoir ce qui différencie deux classes, on peut regarder les coordonnées du vecteur dont les extrémités sont les centres des deux classes. Quelles sont les caractéristiques qui différencient les voitures familiale des voitures citadines ? Les voitures sportives des voitures citadines ?
:::

**Solution**
```{code-cell} ipython3
:tags: ["hide-cell"]
attributs_noms
```
```{code-cell} ipython3
:tags: ["hide-cell"]
np.array(centres[1]) - np.array(centres[3])
```
