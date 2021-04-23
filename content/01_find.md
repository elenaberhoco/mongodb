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

# Premières requêtes en MongoDB

Auteurs/trices : **Julie FRANCOISE, Manon MAHEO et Valentin PENISSON**

## Introduction à MongoDB

Dans un **système de base de données relationnelles**, les données sont stockées par ligne *(appelées n-uplets)* dans des tables *(également appelées relations)*. Le modèle de données relationnel est un modèle très structuré, comportant des attributs typés et des contraintes d'intégrité *(comme par exemple l'unicité des valeurs de la clé primaire)*. Il est nécessaire de faire des jointures sur plusieurs tables afin de tirer des informations pertinentes de la base.

**Dans MongoDB, les données sont modélisées sous forme de document sous un style JSON.** On ne parle plus de tables, ni d'enregistrements mais de collections et de documents. Une collection est un ensemble de documents, c'est l'équivalent d'une table en relationnel. Un document est un enregistrement, une ligne dans le modèle de données relationnel. Ce système de gestion de données nous évite de faire des jointures de tables car toutes les informations nécessaires sont stockées dans un même document.

Tout document appartient à une collection et a un champ appelé "_id" qui identifie le document dans la base de données. Prenons la la base de données étudiants. Voici un exemple de document : 

```javascript
{
    "_id" : ObjectId("56011920de43611b917d773d"),
    "nom" : "Paul",
    "notes" : [ 
        10.0, 
        12.0
    ],
    "sexe" : "M"
}
```

On a une association de clés et de valeurs, un document est équivalent aux objets JSON. Dans ce document, on a accès au nom de l'étudiant, à ses notes *(c'est une **liste de valeurs**, ce type d'attribut n'étant pas disponible dans le modèle relationnel)*, et à son sexe. L'étudiant représenté par ce document, est identifié à l'aide d'une clé "_id". Pour effectuer des requêtes sur une base de données MongoDB et filtrer les données, il est indispensable d'utiliser ces indications clés et valeurs. 

Dans ce chapitre, nous étudierons dans un premier temps [**comment interroger les données d'une base de données MongoDB avec la fonction find**](#find). Ensuite nous regarderons comment effectuer des [requêtes plus complexes, impliquant des **opérateurs de comparaison**](#operateurs). Quelques [**méthodes utiles**](#methodes) pour des requêtes en MongoDB sont données à la fin de ce chapitre ainsi qu'une [**conclusion**](conclusion).

---

(find) = 
## Requêtes d'interrogation et de filtrage des données : la fonction `find`

Pour récupérer des documents stockés dans une collection, il est nécessaire d'utiliser la fonction find.
 
 ```{admonition} Remarque
Toute commande sur une collection intitulée collectionName utilise le préfixe db : "db.collectionName". Il suffit d’y associer la fonction souhaitée pour avoir un résultat. 

En l'occurence, ici la syntaxe de données d'interrogation MongoDB est db.collectionName.find().
```

En MongoDB, il existe deux types de requêtes simples, retournant respectivement **toutes les occurences d'une collection** ou **seulement la première**. 

````{panels}

Retourner toutes les occurences 
d'une collection avec find()
^^^
```javascript
 db.collectionName.find()
 db.collectionName.find({}) 
```

---

Retourner uniquement la première occurence
de la liste de résultats avec findOne()
^^^
```javascript
db.collectionName.findOne()
db.collectionName.findOne({})
```

````

> À noter : Dans les deuxièmes propositions, on a des accolades entre les parenthèses de la fonction. Ces accolades correspondent au *document masque*. Elles sont vides ce qui indique que nous ne posons pas de condition sur les documents à retourner. 

Si l’on souhaite fixer des contraintes sur les documents à retourner, il suffit de passer en argument d’une de ces fonctions un document masque contenant les valeurs souhaitées. La requête suivante retourne tous les documents ayant un champ "x" dont la valeur est "y". En utilisant cette syntaxe, on recherche par exemple les documents de la collection NYfood correspondant à des **boulangeries** *(pour lesquels le champ "cuisine" vaut "Bakery")* **du Bronx** *(pour lesquels le champ "borough" vaut "Bronx")*. Dans cet exemple sur la base de données NYfood, la virgule représente un ET logique entre les contraintes.     

````{tabbed} Syntaxe

```javascript
db.nomDeLaCollection.find({"x":"y"})
```

````
 
````{tabbed} Exemple sur la base de données NYfood

```javascript
db.NYfood.find(
    {"cuisine": "Chinese", "borough": "Bronx"}
)
```

````

Il se peut que pour une clé d'un document, comme par exemple l'adresse d'un restaurant, nous disposons d'un **sous-document** contenant à la fois les coordonnées GPS et l'adresse postale. Si l'on souhaite **poser une condition sur une clé ou plusieurs clés de sous-document**, on utilise alors la syntaxe suivante :

```javascript
db.NYfood.find({"adress.zipcode": "10462"})
```
où adress est le sous-document et zipcode la clé de ce dernier. Dans cet exemple, nous nous intéressons aux restaurants pour lesquels le zipcode est "10462".

Les résultats que obtenus jusqu’à présent sont parfois assez indigestes, notamment parce que toutes les clés sont retournées pour tous les documents. Il est possible de limiter cela en spécifiant les clés à retourner comme second argument de find(). On appelle ça une **projection**.

<dl>
  <dt>Projection</dt>
  <dd>La projection permet de sélectionner les informations à renvoyer. Si, par exemple, je m’intéresse uniquement aux noms des boulangeries du Bronx, je vais     limiter les informations retournées en précisant comme deuxième argument de ma recherche find, la clé name avec la valeur true.</dd>
</dl>

```javascript
db.NYfood.find({"cuisine": "Bakery", "borough": "Bronx"}, {"name": true})
```
> C'est l'équivalent du "SELECT name" en SQL. Jusqu'ici, on utilisais le "SELECT *" *(pour all)* c'est-à-dire qu'on récupérait toutes les valeurs de chaque clé ou de chaque attribut.

```{admonition} Embellissez les résultats de la fonction find ! 
:class: tip

Les résultats de la fonction find() peuvent apparaître désorganisés. MongoDB fournit pretty() qui affiche les résultats sous une forme plus lisible. La syntaxe est la suivante : collectionName.find().pretty() 😉
```

Pour plus de renseignements sur la **fonction find()**, consultez la documentation MongoDB [disponible ici](https://docs.mongodb.com/manual/reference/method/db.collection.find/).

---

(operateurs)= 
## Requêtes plus complexes en utilisant des opérateurs

Les opérateurs se séparent en deux grandes parties : les **opérateurs de comparaison** et les **opérateurs logiques**.

### Opérateurs de comparaison

L'opérateur de comparaison permet de comparer deux élements entre eux. Le tableau suivant l'ensemble des opérateurs de comparaison : 

| Opérateur logique 	| Mot clé en MongoDB 	|
|-	|-	|
| = 	| $eq 	|
| < 	| $lt 	|
| > 	| $gt 	|
| ≤ 	| $lte 	|
| ≥ 	| $gte 	|
| ∈ 	| $in 	|
| ∉ 	| $nin 	|
| négation 	| $ne 	|
| clé existante 	| $exists 	|
| \|.\| 	| $size 	|

Les opérateurs `$eq`, `$lt`, `$gt`, `$lte`, `$gte` s'utilisent de la même façon en MongoDB. Ces opérateurs comparent la valeur d'une variable à une valeur fixe (nombre, booléen, chaine de caractères...).

````{panels}

MongoDB
^^^
```javascript
db.t.find(
    {"a": {$gte : 1}
    }
)
```

---

SQL
^^^
```sql
SELECT *
FROM t
WHERE a >= 1 
```

````

Les opérateurs `$in` et `$nin` s'ulisent de la même façon en MongoDB. Ces opérateurs teste l'existence de la valeur d'une variable dans une liste. Sa façon de l'utiliser en MongoDB est la suivante : 

````{panels}

MongoDB
^^^
```javascript
db.t.find(
    {"a": { $in: ["chaine1", "chaine2"] }
    }
)
```

---

SQL
^^^
```sql
SELECT *
FROM t
WHERE a IN ("chaine1", "chaine2")
```

````

L'opérateur `$exists` vérifie l'existence d'une clé dans un document. Sa syntaxe en MongoDB est : 

```javascript
db.t.find(
    {"a": { $exists: true}
    }
)
```
Cette requête renvera donc les documents ayant le sous-document `a` existant.

Enfin, l'opérateur `$size` permet des récuperer les documents avec des sous-documents d'une certaine taille. Sa syntaxe en MongoDB s'écrit comme suit :

```javascript
db.t.find(
    {"a": { $size: 5}
    }
)
```
Le résultat obtenu est l'ensemble des documents avec le sous-document `a` qui est de taille **5**.

### Opérateurs logiques

Les différents opérateurs logiques en MongoDB sont : `and`, `or`, `not` et `nor`. Ces opérateurs de tester plusieurs conditions simultanément.

#### `and` logique

L'opérateur `and` renvoie les documents qui remplissent l'ensemble des conditions. Pour faire une requête avec un `and` logique en MongoDB, il suffit de séparer par une virgule chaque condition. L'exemple ci-dessous nous montre l'équivalence entre MongoDB et le langage SQL : 

````{panels}

MongoDB
^^^
```javascript
db.t.find(
    {"a": 1, "b": 5}
)
```

---

SQL
^^^
```sql
SELECT *
FROM t
WHERE a = 1 and b = 5
```

````

Le résultat de la requête sera les documents validant les deux conditions suivantes : `a` = **1** et `b` = **5**.

#### `or` logique

L'opérateur `or` permet de renvoyer les documents qui remplissent au moins un des conditions de la requête. Le `or` logique se construit de la manière suivane : `$or : [{condition 1}, ... , {condition i}]`. Voici un exemple faisant le parralèle entre le langage MongoDB et le langage SQL :

````{panels}

MongoDB
^^^
```javascript
db.t.find(
    {$or : [
      {"a": 1},
      {"b": 5}
      ]
    }
)
```

---

SQL
^^^
```sql
SELECT *
FROM t
WHERE a = 1 or b = 5
```

````

Le résultat de la requête sera les documents validant au moins un des deux conditions suivantes : `a` = **1** ou `b` = **5**.

#### `nor` logique

L'opérateur `nor` permet de renvoyer les documents ne validant pas une liste de condition(s). Voici sa syntaxe qui est très semblable à celle de `or` : 

```javascript
db.t.find(
    {$nor : [
      {"a": 1},
      {"b": "blue"}
      ]
    }
)
```
Le résultat de cette requête sera l'ensemble des documents ne contenant pas la valeur **1** pour la variable `a` et **"blue"** pour la variable `b`.

https://docs.mongodb.com/manual/reference/operator/query/

---

(methodes) =
## Méthodes utiles pour des requêtes en MongoDB

### Valeurs distinctes d'un champ : la méthode `distinct`

La méthode `distinct` permet de ne renvoyer que les valeurs distinctes d'un champ ou d'une liste de conditions. C'est l'équivalent du `DISTINCT` en SQL.

````{panels}

MongoDB
^^^
```javascript
db.collectionName.distinct(champ,
    {...}
)
```

---

Notation SQL
^^^
```sql
SELECT DISTINCT(champ)
FROM collectionName
WHERE ...
```

````

La requête ci-dessus permet de renvoyer tous les éléments distincts de `champ` de la collection choisie. Si elle est bien formulée, on devrait obtenir tous les valeurs possibles du champ une fois au maximum.

### Compter le nombre d'éléments : la méthode `count`

La méthode `count` permet de compter le nombre d'éléments ou de documents présents dans une collection. On peut l'utiliser directement sur la collection de base ou bien l'utiliser après avoir exécuter une requête.

````{tabbed} Sur une collection sans requête

```javascript
db.collectionName.count()
```
````

````{tabbed} Sur une collection après requête

```javascript
db.collectionName.find({"a": 1}).count()
```
````

Bien entendu, les résultats seront différents car on n'a pas le même nombre de documents ou d'éléments avant et après une requête.

### Trier la récupération des documents : la méthode `sort`

La méthode `sort` sert à trier, après avoir effectuer une requête, les documents ou les éléments de la base de données à partir d'un des champs. Pour choisir l'ordre, il suffit de mettre `1` pour trier dans un ordre croissant et `-1` pour trier dans un ordre décroissant.

Voici comme ça se présente pour un tri par ordre croissant.
```javascript
db.collectionName.find().sort(
  {"champ" : 1}
)
```

Il est également possible de faire un tri sur plusieurs champs. On peut aussi mettre différents ordres, croissant ou déccroissant.
```javascript
db.collectionName.find().sort(
  {"champ1" : 1, "champ2" : -1}
)
```

On notera qu'il est impossible d'utiliser cette méthode sans faire une requête `find` au préalable qu'elle soit vide ou non.

### Limiter la récupération des documents : la méthode `limit`

On également limiter le nombre de résultats obtenus avec la méthode limit.

```javascript
db.NYfood.find({}).limit(2)
```

---

(conclusion) = 
## Conclusion

