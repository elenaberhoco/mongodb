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
  display_name: base
  language: ''
  name: python3
---

# Requête avec opérateurs

On peut effectuer des requêtes plus complexes à l'aide d'opérateurs. 

````{admonition} Example
:class: tip

Dans la suite, on utilisera la base de données suivante :
```javascript
use Sport
db.athletes.insert({
                      "nom": "Alice Martin",
                      "age": 22,
                      "sexe": "F",
                      "taille": 175,
                      "sport": ["basketball", "volleyball"],
                      "blessures": ["entorse cheville"],
                      "categorie": "amateur"
                   })

db.athletes.insert({
                      "nom": "Bruno Silva",
                      "age": 28,
                      "sexe" : "M",
                      "taille": 182,
                      "sport": ["football"],
                      "blessures": [],
                      "categorie": "professionnel"
                   })

db.athletes.insert({
                      "nom": "Carla Lopez",
                      "age": 19,
                      "sexe": "F",
                      "taille": 160,
                      "sport": ["tennis"],
                      "blessures": ["tendinite épaule", "fracture poignet"],
                      "categorie": "professionnel"
                   })

db.athletes.insert({
                      "nom": "David Chen",
                      "age": 31,
                      "sexe": "M",
                      "taille": 190,
                      "sport": ["basketball"],
                      "categorie": "semi-professionnel"
                   })

db.athletes.insert({
                      "nom": "Emma Dubois",
                      "age": 25,
                      "sexe": "F",
                      "taille": 168,
                      "sport": ["athlétisme", "natation"],
                      "blessures": ["élongation ischio-jambiers"],
                      "categorie": "semi-professionnel"
                   })

db.athletes.insert({
                      "nom": "Alex Legrand",
                      "age": 22,
                      "sexe": "M",
                      "taille": 175,
                      "sport": ["MMA"],
                      "blessures": ["entorse cheville","fracture du nez","élongation ischio-jambiers"]
                   })
```
````

## Opérateurs de comparaison

Les opérateurs de comparaison permettent de comparer deux éléments entre eux. La syntaxe est la suivante :  
```
db.<collection>.find({<key>:{<operator>:<value>}}, <projection>)
```

|  Opérateur  |     Notation mathématique    |    Description    |
|:---:|:---:|:---:|
|  `$eq`  | = |  **eq**ual to  |
|  `$ne`  | ≠ |  **n**ot **e**qual  |
|  `$lt`  | < |  **l**ess **t**han  |
|  `$gt`  | > |  **g**reater **t**han  |
|  `$lte`  | ≤ |  **l**ess **t**han or **e**qual   |
|  `$gte`  | ≥ |  **g**reater **t**han or **e**qual|
|  `$in`  | ∈ |  **in**  |
|  `$nin`  | ∉ |  **n**ot **in**  |
|  `$exists`  | $\exists$ |  **exists**  |

Les opérateurs `$eq`, `$ne`, `$lt`, `$gt`, `$lte` et `$gte` s’utilisent avec des nombres, des booléens, des chaînes de caractères, des dates, des expressions régulières etc.

````{admonition} Example
:class: tip

Pour récupérer le nom de tous les athlètes de plus de 25 ans (non compris), tapez :
```
db.athletes.find({"age":{"$gt":25}},{"nom":1,"_id":0})
```
Pour exclure les athlètes hommes, tapez :
```
db.athletes.find({"sexe":{"$ne":"M"}})
```
Notez que les commandes suivantes sont équivalentes :
```
db.athletes.find({"sexe":{"$eq":"M"}})
db.athletes.find({"sexe":"M"}})
```
````

Il est possible d'enchaîner les opérateurs de comparaison pour extraire des documents en fonction d'un intervalle de valeurs.
````{admonition} Example
:class: tip

Afin de savoir quels sports les athlètes mesurant entre 1m80 et 2m pratiquent, tapez :
```
db.athletes.find({"taille":{"$gte":180,"$lte":200}}, {"sport":1,"_id":0})
```
````

`$nin` étant la négation de `$in`, les deux opérateurs s'utilisent de la même façon. Ils doivent être associés à une liste de valeurs. `$in` (resp. `$nin`) permet de sélectionner (resp. exclure) les documents dont la valeur ou l'une des valeurs d'un champ est égale à au moins l'une des valeur de la liste spécifiée.  
````{admonition} Example
:class: tip

La commande suivante permet d'obtenir l'ensemble des blessures des athlètes amateurs et semi-professionnels :
```
db.athletes.find({"categorie":
                     {"$in":["amateur","semi-professionnel"]}
                  },
                  {"blessures":1}
)
```
`$in` et `$nin` peuvent aussi être appliqués à des champs dont la valeur est une liste. On peut par exemple récupérer le nom des athlètes qui ne pratiquent ni le basket ni le karaté ainsi :
```
db.athletes.find({"sport":{"$nin":["basketball","karaté"]}}, {"nom":1})
```
````

:::{caution}
Vous pouvez avoir quelques surprises lorsque vous utilisez des opérateurs de négation, tels que `$nin`, `$nor` et `$not`. Par exemple, en indiquant avec `$nin` que vous souhaitez accéder aux documents pour lesquels tel champ ne contient pas telles valeurs, les documents qui ne contiennent pas ledit champ ou pour lequel le champ est vide seront aussi retournés. On ne peut en effet pas dire que ces champs vérifient l'égalité que vous souhaitez exclure ... puisque qu'ils ne vérifient aucune égalité. Pour vous en convaincre, voir l'exemple en suivant. Par conséquence, si vous souhaitez également exclure les documents qui ne possèdent par le champ utilisé dans le filtre ou pour lesquels le champ est vide, vous devez le faire explicitement.  
:::
````{admonition} Example
:class: tip

Exécutez la commande suivante :
```
db.athletes.find({"categorie":{"$nin":["amateur"]}})
```
Que remarquez-vous ?
````

Enfin, l'opérateur `$exists` permet de vérifier l'existence d'une clé dans un document. `$exists` peut prendre la valeur `true` ou `false`.
````{admonition} Example
:class: tip

Existe-t-il des documents qui n'ont pas de champ `blessures` ?
```
db.athletes.find({"blessures":{"$exists":false}})
``` 
````

## Opérateurs logique

Les opérateurs logiques permettent de tester plusieurs conditions simultanément. 

|  Opérateur  |    Description    |
|:---:|:---|
|  `$and` ou virgule  |  Extraire les documents qui vérifient toutes les conditions de la requête |
|  `$or`  |  Extraire les documents qui vérifient l'une des conditions de la requête |
|  `$nor`  |  Extraire les documents qui ne vérifient aucune des conditions de la requête |
|  `$not`  |  Extraire les documents qui ne vérifient pas la condition spécifiée  |

La syntaxe pour `$and`, `$or` et `$nor` est la suivante :  
```
db.<collection>.find({<operator>:[<filter1>,<filter2>,...]}, <projection>)
```

Comme vu dans le chapitre précédent, pour extraire les documents qui vérifient un ensemble de conditions, il suffit de séparer lesdites conditions par une virgule. Toutefois, vous pouvez également utiliser l'opérateur `$and`.
````{admonition} Example
:class: tip

Pour retourner le nom des athlètes femmes de plus d'1m65 vous pouvez tapez au choix :
```
db.athletes.find({"sexe":"F","taille":{"$gt":165}}, {"nom":1})
``` 
ou
```javascript
db.athletes.find({"$and": 
                     [
                       {"sexe":"F"},
                       {"taille":{"$gt":165}}
                     ]
                 },
                 {"nom":1}
)
```
````

L'opérateur `$or` permet d'extraire les documents qui vérifient au moins l'une des conditions spécifiées.
````{admonition} Example
:class: tip

Pour obtenir des informations sur les athlètes amateurs ou semi-professionnels, tapez :
```
db.athletes.find({"$or":
                     [
                       {"categorie":"amateur"},
                       {"categorie":"semi-professionnel"}
                     ]
                 })
``` 
N'a-t-on pas déjà effectué une requête similaire à l'aide d'un autre opérateur ? MongoDB recommande de privilégier la première option lorsque les conditions d'égalité sont appliquées à un même champ, comme c'est le cas ici avec `categorie`. Cependant, cet exemple permet d'illustrer le fait qu'on peut implémenter une même requête de différentes façons. 
````

L'opérateur `$nor` permet d'extraire les documents qui ne vérifient aucune des conditions spécifiées.
````{admonition} Example
:class: tip

Pour trouver l'ensemble des athlètes qui ne jouent pas au football et qui ne sont pas des femmes, tapez :
```
db.athletes.find({"$nor":
                     [
                       {"sport":"football"},
                       {"sexe":"F"}
                     ]
                 })
```
````

Enfin, `$not` permet d'extraire les documents qui ne vérifient pas la condition spécifiée. La syntaxe est la suivante :
```
db.<collection>.find({<key>:{"$not":{<operator_expression>}}}, <projection>)
```
où `<operator_expression> = {<operator>:<value>, ...}`

````{admonition} Example
:class: tip

La commande suivante permet d'exclure les athlètes qui ont entre 25 et 30 ans :
```
db.athletes.find({"age":
                     {
                       "$not":
                          {"$gte":25,"$lt":30}
                     }
                 })
```
Exclure les athlètes qui ont entre 25 et 30 ans revient entre à extraire les athlètes de moins de 25 ans ou de plus de 30 ans. Sauriez-vous écrire la requête correspondante ?
````

## Requête sur des listes

Les requêtes sur les listes ont quelques spécificités.

|  Opérateur  |    Description    |
|:---:|:---|
|  `$size`  |  Taille de la liste  |
|  `$elemMatch`  |  Extraire les documents pour lesquels le champ de type liste a au moins un élément qui correspond à tous les critères spécifiés  |

À FAIRE !!

## Exrcice



