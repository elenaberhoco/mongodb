---
jupytext:
  cell_metadata_filter: -all
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.12
    jupytext_version: 1.9.1
kernelspec:
  display_name: IMongo
  language: ''
  name: imongo
---

# Les requêtes d'agrégation

## Regroupements

* Auteurs/trices : Salomé Vavasseur, Jeremy Castrique, Dimitri Nojac

Cette section traite de :
  * somme, count, max, min, avec ou sans groupe

  Le fichier que vous devez modifier pour ce chapitre est `mongo_book/content/05_agreg.md`.

##  Successions d'étapes d'agrégation 

* Auteurs/trices : Marine BINARD, Yann CAUSEUR, Arthur CONAS


### Introduction
Les successions d'étapes d'agrégation vont permettre d'obtenir des requêtes proches de ce qu'on peut trouver en SQL.
Contrairement à SQL où l'ordre est pré-défini (`SELECT FROM WHERE ORDER BY`), ici ce n'est pas le cas. Il n'empêche que **l'ordre dans lequel on place
nos étapes est crucial.**

Nos étapes peuvent toutes être effectuées une à une et indépendamment. En fait, à l'intérieur de notre `db.coll.aggregate([])`, il y aura notre liste d'étapes,
contenues dans des crochets et séparées par des virgules, qui s'effectueront sur les données que **l'étape d'avant aura rendue.**

Il peut donc être intéressant d'éxécuter le code étape par étape pour savoir sur quelles données on travaille à un moment donné.

Commençons par regarder ce que peut faire chaque étape.


### <center> Project </center>
***Pourquoi l'utiliser ?***  
Il peut arriver lors d'une requête d'agrégation de vouloir créer de nouvelles variables par exemple, pour des calculs. La commande `$project` permet donc de créer de nouvelles variables. Néanmoins, il faut faire attention, 
lorsque l'on crée une nouvelle variable dans une requête d'agrégation.
 Tous les attributs déjà existants pour les documents d'une collection ne sont plus mémorisés. Donc, si on veut créer une nouvelle variable, tout en gardant celles déjà existantes, il faut le mentionner dans le `$project`. 

***Comment ça fonctionne ?*** 

**Syntaxe** :  
```
db.coll.aggregate( 
  [
    {$project : {<nom_nouv_att1> : <val_att1>, <nom_nouv_att2> : <val_att2>, ... }}
  ]
)
```

Le fait de vouloir garder un attribut déjà existant fonctionne de la même façon que la création, il faut donc renommer la variable existante.   

***Exemple :***  
```{code-cell}
use food
```


```{code-cell}
db.NYfood.aggregate( 
  [
    {$project: {"n_notes" : {$size : '$grades'}}}
  ]
)
```
Sur l’exemple ci-dessus, on vient créer une variable n_notes qui prend pour valeur la taille de la liste `grades` 
(qui contient les différentes notes attribuées aux restaurants).
On cherche donc, ici, à compter le nombre de notes attribué à chaque restaurant.
 Mais tous les autres attributs du restaurant sont effacés. Par la suite,
 on ne pourra donc retrouver que le nombre de notes attribué et non le quartier 
 ou le type de restaurant. 
Si on veut afficher le quartier en question, on doit le préciser tel que :
```{code-cell}
db.NYfood.aggregate( 
  [
    {$project: {"n_notes" : {$size : '$grades'}, quartier :'$borough'}}
  ]
)
```

Avec cette requête, on peut voir le quartier du restaurant. Par ailleurs, la variable `borough` 
a été renommée `quartier`. On peut également conserver cette 
variable sans la renommer avec cette syntaxe.
```{code-cell}
db.NYfood.aggregate( 
  [
    {$project: {"n_notes" : {$size : '$grades'}, borough : 1}}
  ]
)
```
***Traduction SQL :***

L'équivalent en SQL de la commande `$project` est l'étape `SELECT` et `AS` qui 
permettent de créer de nouvelles variables. Par contre, en SQL,
l'étape `AS` est facultative, la nouvelle variable prendra 
comme nom la formule du calcul. En MongoDB, elle est obligatoire ! 
Si on ne précise pas le nom de la nouvelle variable, cela affichera 
une erreur. Pour la traduction SQL de l'exemple précédent, il convient de faire attention.
Pour rappel, les listes n'existent pas en SQL ! D'où la nécessité
dans certains moments de faire des calculs verticaux, ce qui n'est pas nécéssaire.
Dans notre cas, en SQL l'attribut `grades` serait une table à part entière (avec toutes les notes `grade`, une clé étrangère faisant référence au restaurant)
Il faudrait donc faire une jointure sur celle-ci puis grouper par restaurant (en imaginant qu'il existe un ID pour chaque restaurant)
L'exemple serait donc:   

 ```sql
 SELECT borough
 FROM NYfood NATURAL JOIN grades
 GROUP BY restoID
 HAVING COUNT(grade) AS "n_notes"
 ```
 
### <center> Sort </center>

***Pourquoi l'utiliser ?***

Comme dans la plus part des langages de bases de données, MongoDB ne stocke pas les documents dans une collection dans un ordre 
en particulier. C'est pourquoi l'étape `sort` (tri en français) va permettre
 de trier l'ensemble de tous les documents d'entrée afin de les renvoyer dans
 l'ordre choisi par l'utilisateur. Nous pouvons les trier dans l'ordre croissant,
 décroissant, chronologique ou bien alphabétique selon le type du champ
 souhaitant être trié. 
Il est possible de trier sur plusieurs champs à la fois, mais dans ce cas l'ordre de tri est évalué de gauche à droite. 
Le `$sort` est finalement l'équivalent du `ORDER BY` en SQL.

***Comment ça fonctionne ?***

**Syntaxe** :  
```
db.coll.aggregate(
	[
	 {$sort: {<champ1>: <sort order>, <champ2>: <sort order> ...}}
	]
)
```

Le `<sort order>` peut prendre la valeur : 1 (croissant), -1 (décroissant) ou encore `{$meta: "textScore"}`(il s'agit d'un tri de métadonnées textScore calculées dans l'ordre décroissant).

***Exemples :***

Attention à bien prendre en compte le fait que lors du 
tri sur un champ contenant des valeurs en double (ou non unique),
 les documents contenant ces valeurs peuvent être renvoyés dans 
 n'importe quel ordre.
```{code-cell}
db.NYfood.aggregate(
   [
	 {$sort : {borough : 1}}
   ]
)
```
***Traduction SQL :***

```sql
SELECT borough
FROM NYfood 
ORDER BY borough
```

En effet, dans l'exemple ci-dessus, le champ quartier n'est 
pas un champ avec des valeurs uniques. Si un ordre de tri 
cohérent est souhaité, il est important d'au moins inclure 
un champ dans votre tri qui contient des valeurs uniques. 
Généralement, le moyen le plus simple de garantir cela consiste 
à inclure le champ _id dans la requête de tri.
```{code-cell}
db.NYfood.aggregate(
   [
     {$sort : {borough : 1, _id : 1}}
   ]
)
```
Cette fois ci, la requête affichera l'ensemble 
de la collection avec les noms de quartier 
affichés par ordre alphabétique. Les collections 
du quartier de "Bronx" seront les premières à être affichées, 
puis ensuite l'ordre par identifiant sera conservé 
lorsque le nom de quartier sera le même pour plusieurs collections.

***Traduction SQL :***
 
```sql
SELECT borough
FROM NYfood 
ORDER BY borough, _id
```

### <center> Limit </center>
***Pourquoi l'utiliser ?***

L'étape `$limit` va simplement permettre de 
limiter le nombre de documents voulant être 
affichés par la requête. Il n'y a pas grand 
intérêt à utiliser le limit tout seul. Généralement, 
il est utilisé avec l'étape `$sort` vu précédemment.

***Comment ça fonctionne ?***

**Syntaxe** :  
```
db.coll.aggregate(
	[
	 {$limit : 5} 
	]
)
```
L'argument qui est pris par le `$limit` est toujours
 un entier positif, qui va déterminer le nombre 
 de collections que l'on souhaite afficher.

***Exemple***

Dans cet exemple, on souhaite afficher les 3 quartiers 
possédant le plus de restaurants.
```{code-cell}
db.NYfood.aggregate(
	[
     {$group: {_id: "$borough", nb: {$sum: 1}}},
     {$sort: {nb: -1}},
     {$limit: 3}
	]
) 
```					  
On remarque ici que nous ne pouvons pas utiliser 
l'étape `$limit` seul sans le sort.
 Nous avons d'abord besoin de trier le nombre 
 de restaurants par ordre décroissant puis enfin  
 préciser que nous souhaitons obtenir seulement les 
 3 premiers quartiers contenant le plus de restaurants.
 
***Traduction SQL :***
```sql
SELECT count(borough)
FROM NYfood 
GROUP BY borough
ORDER BY count(borough) desc
LIMIT 3
```

### <center> Match  </center>

***Pourquoi l'utiliser***

`$match` peut être utilisé comme un filtre, avec une condition. On pourrait le mettre n'importe où dans notre requête mais il est particulièrement intéressant en début ou en fin de requête.


***Comment ça fonctionne ?***

**Syntaxe** : 

Le `$match` est un requête du type de celles qu'on passe à `find`.

***Exemple :***  
```{code-cell}
db.NYfood.aggregate( 
  [  
   {$match: {"borough": 'Brooklyn'}},
   {$unwind: "$grades"},
   {$group : {_id: "$grades.grade",
       n:{$sum:1}
            }
          },
    {$match:{n:{$gt:1000}}},
  ]
)
``` 



```SQL
SELECT COUNT(grade) as n
FROM NYfood
WHERE Borough='Brooklyn'
GROUP BY grade
HAVING n > 1000
``` 
Ici le premier `$match` sert comme un `WHERE`, 
et le deuxième comme un `HAVING` en SQL.

Dans tous les cas, le `$match` fait une sélection sur le jeu de données en fonction d'une condition, au moment où il est placé.

Si le `$match` est au début, il fera une sélection sur l'ensemble des données (ici `NYfood`) mais n'aura pas accès aux opérations qui sont effectuées après (comme le `$group` dans notre cas).
C'est pour cela qu'on utilise aussi le `$match` plus tard, pour avoir accès aux données créées avec nos bouts de requêtes précédents, ce qui permet ici d'avoir accès au `n`.
Cependant, ce dernier `$match` n'a pas accès à toute la base de données `NYfood` et n'agit que sur les résultats des requêtes précédentes.

### <center> Unwind </center>

***Pourquoi l'utiliser ?*** 
 
Il arrive que les documents de certaines collections possèdent pour attribut une liste. Lorsque l'on effectue une requête d'agrégation, il peut être nécéssaire d'agir non pas sur la liste mais sur chaque élément de la liste. Pour cela, on utilise la commande `$unwind`. Elle permet, pour chaque élément de la liste, de dupliquer le document pour chaque valeur de la liste. 

***Comment ça fonctionne ?***

**Syntaxe** :
```
db.coll.aggregate( 
  [
   {$unwind : "$att"}}
  ]
)
```
En général, un `$unwid` seul n'a peu d'intérêt.`$att` est une liste de taille 10 que la collection comporte 1000 individus, la requête d'exemple renverra un résultat de 10 000 lignes (10 * 1000)

***Exemple***
```{code-cell}
db.NYfood.aggregate( 
  [
   {$unwind :"$grades"},
   {$group: {_id : '$grades.grade', 
             n: {$sum:1}}
    },
  ]
)

```
Voici un exemple concret d'utilisation d'un `$unwind`. Dans la requête, on cherche à compter le nombre de A ayant été attribués à l'ensemble des restaurants de la collection, puis le nombre de B, C .... 
Pour que cette requête fonctionne, le `$unwind` est obligatoire, sinon on considère la liste entière des notes et on ne peut donc pas compter. 
 
 ***Traduction SQL :*** 
 
Il n'existe pas réellement d'équivalent SQL au `$unwind`. Néanmoins, il se rapproche d'une opération de jointure sans aucun filtre.

### Quelques requêtes pour tout comprendre
Afin d'illustrer le fonctionnement pas à pas, découpons une requête en détail.
Pour cet exemple, on veut  **les 3 notes les plus données dans les restaurants du quartier de Brooklyn**.
La première étape naturelle est de sélectionner les restaurants présents uniquement dans le quartier de Brooklyn.
Pour cela on utilise `$match`, qui retourne uniquement les restaurants de Brooklyn.
```
db.NYfood.aggregate(
	[
     {$match: {"borough": "Brooklyn"}},
    	]
) 
```
Dans un second temps, il faut voir que pour récupérer les différentes valeurs de notes, il faut acceder à chaque élément de la liste et non la liste entière. Pour y accéder, il faut utiliser la commande `$unwind`, qui rendra donc à cette étape tous les restaurants de Brooklyn associés à une note qu'il a obtenu (Attention, cela retourne beaucoup de résultats : nombre de restaurants * nombre de notes)

```
db.NYfood.aggregate(
	[
     {$match: {"borough": "Brooklyn"}},
     {$unwind: "$grades"},
    ]
) 
```
Ensuite, pour savoir quelle note a été la plus attribuée, il faut grouper pour chaque valeur de note. On utilise donc un `$group` sur l'attribut `$grades.grade$ (accessible grâce a `$unwind`). Puis, on décide de compter le nombre d'itérations de chaque note stockée dans la variable `nb`. Cette étape nous retourne donc le nombre de fois où chaque note a été attribuée à un restaurant.
```
db.NYfood.aggregate(
	[
     {$match: {"borough": "Brooklyn"}},
     {$unwind: "$grades"},
     {$group: {_id: "$grades.grade", nb: {$sum: 1}}},
    ]
) 
```
Nous sommes donc tout proches du résultat espéré. Il reste maintenant à trier les résultats par ordre décroissant, afin d'avoir les notes les plus données au début : `{$sort: {nb: -1}}`. Mais comme l'énoncé le précise, on souhaite afficher uniquement les 3 notes les plus données. Etant donné que les notes sont triées, il faut seulement préciser : `{$limit: 3}`.
```
db.NYfood.aggregate(
	[
     {$match: {"borough": "Brooklyn"}},
     {$unwind: "$grades"},
     {$group: {_id: "$grades.grade", nb: {$sum: 1}}},
     {$sort: {nb: -1}},
     {$limit: 3}
    ]
) 
```
On obtient bien, avec cette requête, les 3 notes les plus attribuées aux restaurants de Brooklyn !

``` {code-cell}
 db.NYfood.aggregate(
	[

     {$project: {taille: {$size: "$grades"}}},
     {$match :{taille:{$gt:2}}},
     {$group: {_id: null,
      nb_min: {$min: "$taille"},
      nb_max: {$max: "$taille"}}
                        },         
    ]
) 
```
Dans cette deuxième requête, on montre bien ici qu'il n'y a pas d'ordre pré-défini d'étape, et ici le `$match` n'est ni au début de la requête, ni à la fin.

Expliquons cette requête (qui n'a pas beaucoup d'intérêt pratique).

* `$project` : création de la variable taille, qui correspond au nombre de notes données à un restaurant.
* `$match` : Dans le tableau rendu précédemment, on ne prend que les restaurants ayant plus de 2 notes.
* `$group` : Sur le résultat de la requête précédente, on groupe tout les restaurants (_id : null), et on regarde le nombre minimum et maximum de notes attribuées
à un restaurant. (Ayant sélectionné les individus supérieurs à deux, le minimum ne pouvait être que 3 ou plus.

En SQL, on aurait :
```sql
SELECT COUNT(*) AS taille, MAX(taille),MIN(taille)
FROM NYfood
WHERE taille>=2
```
**Résultat final : Le nombre minimum et maximum de notes attribué aux restaurants ayant au moins deux notes.**

