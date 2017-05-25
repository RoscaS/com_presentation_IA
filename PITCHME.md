## 1 Tri-Bulle

Lors de chaque parcours, l'élément actuelement pointé par la variable d'itération `j`, si il est plus petit que l'élément à sa gauche `j-1` est échangé avec ce dernier.

- poule		 |
- JavaScript |
- Kotlin     |
- Go         |
- vache      |


---?video=00illustrations/AI.mp4


---

<table>
  <tr>
    <th>Firstname</th>
    <th>Lastname</th> 
    <th>Age</th>
  </tr>
  <tr>
    <td>Jill</td>
    <td>Smith</td>
    <td>50</td>
  </tr>
  <tr class="fragment">
    <td>Eve</td>
    <td>Jackson</td>
    <td>94</td>
  </tr>
  <tr class="fragment">
    <td>John</td>
    <td>Doe</td>
    <td>80</td>
  </tr>
</table>

---

<img src="/00illustrations/triBulle-flag.gif" height="500">

---

## Simple

- Tous les parcours commencent à la fin du tableau `n-1`.
 
- Lors de chaque parcours, l'élément actuelement pointé par la variable d'itération `j`, si il est plus petit que l'élément à sa gauche `j-1` est échangé avec ce dernier.

+++

<img src="/00illustrations/Echanger.png" align="" height="150" float="right">

+++

```c++
void echange(int *t, int idx)
{
    int temp = t[idx];
    t[idx]   = t[idx-1];
    t[idx-1] = temp;
}
```

+++

- Si l'élément à sa gauche `j-1` est plus petit, il n'y a pas d'échange.

- L'élément le plus petit se retrouve au début du tableau à la fin du premier parcours.

- Tant que l'élément pointé par la variable d'itération est plus petit que `j-1`, il se déplace vers le début du tableau.

- Au second parcours, le second élément le plus petit se retrouvera trié à droite de l'élément le plus petit et ainsi de suite jusqu'au tri complet du tableau.

+++

<img src="/00illustrations/Tri_Bulle.png" align="" height="250" float="right">

+++

```c++
// tri bulle simple

void triBulle(int *t, int g, int d)
{
    for (int i = 1; i <= d-g; i++)
    {
        for (int j = d; j >= g+1; j--)
        {
            if (t[j] < t[j-1]) { echange(t,j); }
        }
    }
}
```

+++

### Complexité 
La boucle extérieur qui est responsable du nombre
de parcours du tableau a sa variable d'itération qui est égale au nombre de cases du tableau `-1`.

La boucle intérieur qui est responsable du nombre de comparaisons par parcours a sa variable d'itération
que est également égale au nombre de cases du tableau `-1`.

(N-1)*(N-1) = (N-1)^2 comparaisons La complexité de cet version de l'algorithme est de: **O(N^2)**

+++

### Itératif

Nous pouvons dire qu'après i parcours du tableau, les i plus petits éléments seront triés. Il n'est donc plus nécéssaire que les parcours itèrent sur ces cases ce qui suggère une première amélioration:

+++

```c++
// tri Bulle itératif:

void triBulle(int tab[], int debut, int fin){
    for (int i = 1; i <= fin; i++){
        for (int j = fin; j >= debut+i; j--){
            display(tab, i, j, 8);
            if (tab[j] < tab[j-1]){
                swap(tab, j, j-1);
            }
        }
    }
}
```

Plusieurs éléments peuvent remonter lors d'un même parcours. Avant que l'élément le plus petit restant ne remonte jusqu'à sa place définitive, tous les éléments qui ont à leur gauche (j-1) un élément plus petit remontent au moins d'une case.

+++

### Flag

Le tableau est donc suceptible d'être trié avant les (N-1) parcours. Il serait donc profitable de cesser les parcours dés que le tableau est trié surtout dans le cas d'un tableau qui est totalement trié dés le début. C'est ce que nous permet la prochaine amélioration qui stop la fonction si un parcours entier a été fait sans aucun échange:

+++

[<a href=""><img src="/00illustrations/triBulle_Flag.png" align="" height="400" float="right"></a>](00illustrations/triBulle_Flag.png)

+++

```c++
// triBulle-flag.cpp

void triBulle(int *t, int g, int d)
{
    int j = 1;
    bool flag = true;
    while (flag)
    {
        flag = false;
        for (int i = d; i >= g+j; i--)
        {
            if (t[i] < t[i-1])
            {
                echange(t,i);
                flag = true;
            }
        }
        j++;
    }
}
```
+++

### Complexité 

Le nombre maximum d'échanges reste le même (N(N-1))/2
mais la complexité de l'algorithme a augmenté, ainsi
que sa durée d'execution.
Cette version n'est donc interessante que pour des
tableaux très proche d'un tableau ordonné
dés le début.

**O(N^2)**

+++

### Étude de la stabilité
Si deux éléments d'indice `i` et `j` avec `i < j`.
tout élément de clé strictement inférieur se
placera à leur gauche. Ils se trouveront donc
dans des cases d'indice `k` et `k+1`. Lors de la
comparaison des clés des ces deux éléments,
**ils ne seront donc pas échangés** et donc l'ordre
de leurs emplacements d'origine est respecté.
L'élément d'indice `i` se trouvera dans la case
`k` et celui d'indice `j > i` dans la case d'indice
`k + 1`.  **La métode est donc stable**.

+++

Cet algorithme possède une assymétrie particulière:
Un seul élément de petite clé mal classé du côté
des éléments de grande clé, d'un tableau presque
trié prend sa place (début) après un seul parcours du
tableau. Par contre un élément de grande clé, mal
classé du coté des petites clés descend (vers la fin)
à sa place à raison d'une case par parcours.
Le tableau suivant:

+++

```c++
 [12] [18] [42] [44] [55] [67] [94] [6]
Debut                               Fin
```


...est trié en deux parcours en utilisant la fonction
triBulle itératif + flag. Un parcours pour faire
monter (vers le début) le clé 6 dans sa case
définitive et un autre parcours pour constater que
le tableau est trié.
Le tableau suivant quant à lui...

+++

```c++
 [94] [6] [12] [18] [42] [44] [55] [67]
Debut                              Fin
```

... nécessite sept parcours pour obtenir un tri
complet: Les sept valeurs de clé plus petites
montent à leur place à raison d'une case par
parcours.

Cette asymétrie suggère une troisième
amélioration de l'algorithme. Pour compenser
l'asymétrie décrite, le sens de deux parcours
consécutifs du tableau est alterné.

---

## 2 Tri-Shaker

Pour compenser
l'asymétrie décrite, le sens de deux parcours
consécutifs du tableau est alterné.

+++

[<a href=""><img src="/00illustrations/triShaker.png" align="" height="600" float="right"></a>]()

+++

```c++

// triShaker.cpp

void swap(int tab[], int a, int b){
    int temp = tab[a];
    tab[a]   = tab[b];
    tab[b]   = temp;
}

void triShaker(int tab[], int g, int d){
    int ptrG = g+1;
    int ptrD   = d;
    int derEch   = d;

    cout << "derEch start: " << derEch << endl;

    while (ptrG <= ptrD){

        cout << "ptrG: " << ptrG
             << "\tptrD: " << ptrD
             << "\tderEch: " << derEch
             << endl;

        for (int i = ptrD; i >= ptrG; i--){
            if (tab[i] < tab[i-1]){
                swap(tab, i, i-1);
                derEch = i;
            }
        }
        cout << "derEch aller: " << derEch << endl;

        ptrG = derEch + 1;
        for (int i = ptrG; i <= ptrD; i++){
            if (tab[i] < tab[i-1]){
                swap(tab, i, i-1);
                derEch = i;
            }
        }
        ptrD = derEch -1;

        cout << "derEch retour: " << derEch << endl;
    }
}
```
+++

A la fin d'un parcours, les "pointeurs" `ptrDebut` et `ptrFin` contiennent les niveaux entre lesquels le prochain parcours sera effectué. Ces pointeurs sont maj en tenant compte du niveau du dernier échange `derEch` effectué lors d'un parcours.
Ce tri prend fin quand les deux variables `ptrDebut` et `ptrFin` sont égales.

+++

### Conclusion sur les améliorations

Toutes les améliorations n'ont pas modifié le nombre d'échanges à faire (_logique_) et réduisent uniquement le nombre de comparaisons redondantes. Un échange (permutation) est généralement une opération **plus couteuse** qu'une comparaison de de clé. Ce qui fait que si on ne concidère que le pire des cas pour évaluer la complexité des différentes améliorations on se retrouve toujours avec du **O(n^2)**.

---

## 3. Tri par extraction (Selection Sort)

[<a href="https://visualgo.net/en/sorting"><img src="/00illustrations/triExtract.gif" align="" height="600" float="right"></a>]()

A chaque tour, l'algorithme trouve la valeur la plus petite et la place à sa position définitive.

+++

#### initial
```c++
 G                                 D
[46] [12] [53] [15] [33] [1] [10] [8]
```
#### tour 1
```c++
 G                                 D
[1] [12] [53] [15] [33] [46] [10] [8]
// valeur la plus petite: 1 change de 
// place avec t[G]
```

+++

```c++
     G                             D
[1] [12] [53] [15] [33] [46] [10] [8]
// la premiere case est maintenant definitive
// G se deplace d'une case a droite.
```

#### tour 2
```c++
     G                             D
[1] [8] [53] [15] [33] [46] [10] [12]
// valeur la plus petite: 8 change 
// de place avec t[G]
```

+++

```c++
          G                        D
[1] [8] [53] [15] [33] [46] [10] [12]
// les deux premieres cases sont maintenant 
// definitives, G se deplace d'une case vers
// la droite.
```

+++

> Il ne sert à rien d'itérer sur tout le tableau à chaque tour,
> les valeurs de gauche, une fois placées le sont définitivement.
> Gauche est incrémenté de 1 à chaque nouveau tour.

On peut donc diviser ce problème en 3 parties distinctes:
1. une fonction échange()
2. une fonction placerMinimumAGauche()
3. triExtraction()

+++

[<a href="https://visualgo.net/en/sorting"><img src="/00illustrations/Echanger.png" align="" height="150" float="right"></a>]()

```c++
void echanger(int *t, int a, int b)
{
    int temp = t[a];
    t[a] = t[b];
    t[b] = temp;
}
```

+++

[<a href="https://visualgo.net/en/sorting"><img src="/00illustrations/placerMinimumAGauche.png" align="" height="250" float="right"></a>]()

```c++
void placerMinGauche(int *t, int g, int d)
{
    int idxMin  = g;
    for (int i = g+1; i <= d; ++i)
    {
        if (t[i] < t[idxMin])
        {
            idxMin = i;
        }
    }
    echanger(t, g, idxMin);
}
```
+++
 
[<a href="https://visualgo.net/en/sorting"><img src="/00illustrations/triExtraction.png" align="" height="150" float="right"></a>]()

```c++
void triExtract(int *t, int g, int d)
{
    for (int i = g; i < d; ++i)
    {
        placerMinGauche(t, g+i, d);
    }
}
```
+++

> Lorsque les N-1 éléments ayant les clés les plus petites sont à leur place, le dernier élément qui a la plus grande clé l'est aussi.

+++

### Complexité:
L'algorithme `placerMinimumAGauche()` est exécuté `n-1` fois pour un tableau de `n` éléments et ceci indépendamment de l'ordre initial des clés. A chaque exécution, cet algorithme effectue Droite - Gauche [- (nombre de parcours déja effectués ?)] comparaisons.  

**C** = `n-1 + n-2 + ... + 3 + 2 + 1` = `(n(n-1))/2`  

Le tri par extraction est aussi lent que le tri bulle dans le cas d'un tableau à clés aléatoirement dispersées. Des différences en plus ou en moins peuvent apparaître dans des cas particuliers. Le tri par extraction est innefficace sur un tableau déjà ordonné puisqu'il n'est ps capable de s'en appercevoir.

### Stabilité

[<a href="https://visualgo.net/en/sorting"><img src="/00illustrations/triExtractStab.gif" align="" height="250" float="right"></a>]()

Le tableau suivant comporte deux clés identiques:

```c++
[15] [15] [37] [21] [13] [18] [45]  // Clé
[ A] [ B] [ C] [ D] [ E] [ F] [ G]  // Information
  1    2    3    4    5    6    7   // Indice
```

L'extraction du premier minimum donne:

```c++
[13] [15] [37] [21] [15] [18] [45]  // Clé
[ E] [ B] [ C] [ D] [ A] [ F] [ G]  // Information
  1    2    3    4    5    6    7   // Indice
```

Les deux éléments de même clé 15 ont été échangés l'un par rapport à l'autre. Après exécution de l'algorithme, le tableau ordonné devient:

```c++
[13] [15] [15] [18] [21] [37] [45]  // Clé
[ E] [ B] [ A] [ F] [ D] [ C] [ G]  // Information
  1    2    3    4    5    6    7   // Indice
```

Cet exemple montre que le tri par extraction n'est **pas stable**.


## 4. Tri par insertion (Insert Sort)

[<a href="https://visualgo.net/en/sorting"><img src="/00illustrations/triInsert.gif" align="" height="600" float="right"></a>]()

```c++
void insertion(int *t, int g, int idx)
{
    int j = idx-1;
    int temp = t[idx];
    while ((t[j] > temp) && (j >= g))
    {
        t[j+1] = t[j];
        j--;
    }
    t[j+1] = temp;
}

void triInsert(int *t, int g, int d)
{
    for (int i = g+1; i <= d; ++i)
    {
        insertion(t,g,i);   
    }
}
```


