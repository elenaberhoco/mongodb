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

Dans un **système de base de données relationnelles**,les données sont stockées par ligne *(appelées n-uplets)* dans des tables *(également appelées relations)*. Le modèle de données relationnel est un modèle très structuré, comportant des attributs typés et des contraintes d'intégrité *(comme par exemple l'unicité des valeurs de la clé primaire)*. Il est nécessaire de faire des jointures sur plusieurs tables afin de tirer des informations pertinentes de la base.

**Dans MongoDB, les données sont modélisées sous forme de document sous un style JSON.** On ne parle plus de tables, ni d'enregistrements mais de collections et de documents. Ce système de gestion de données nous évite de faire des jointures de tables car toutes les informations nécessaires sont stockées dans un même document.

Tout document appartient à une collection et a un champ appelé "_id" qui identifie le document dans la base de données. Prenons Voici un exemple de document : 

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

> Dans ce chapitre, nous étudierons **comment filtrer les données d'une base de données MongoDB avec la fonction find**. Ensuite nous regarderons comment effectuer des requêtes plus complexes, **impliquant des opérateurs de comparaison**. Quelques **méthodes utiles** pour des requêtes en MongoDB sont données à la fin de ce chapitre.

Auteurs/trices : **Julie FRANCOISE, Manon MAHEO et Valentin PENISSON**

---

## Requêtes d'interrogation et de filtrage des données : la fonction `find`

Pour récupérer des documents stockés dans une collection, il est nécessaire d'utiliser la fonction find.
 
 ```{admonition} Remarque
Toute commande sur une collection intitulée collectionName utilise le préfixe db : "db.collectionName". Il suffit d’y associer la fonction souhaitée pour avoir un résultat. 

En l'occurence, ici la synthaxe de données d'interrogation MongoDB est db.collectionName.find().
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

Si l’on souhaite fixer des contraintes sur les documents à retourner, il suffit de passer en argument d’une de ces fonctions un document masque contenant les valeurs souhaitées. Par exemple, la requête suivante retourne tous les documents ayant un champ "x" dont la valeur est "y".

```javascript
db.nomDeLaCollection.find({"x":"y"})
```

Prenons comme exemple la base de données NYfood.   

````{tabbed} Syntaxe

```mongoDB
db.nomDeLaCollection.find({"x":"y"})
```
````
 
````{tabbed} Exemple sur la base de données NYfood

```mongoDB
db.NYfood.find({"cuisine":"Bakery"})
```

````

Projection

La projection permet de sélectionner les informations à renvoyer. Si, par exemple, je m’intéresse uniquement au titre du film, à son année de sortie et aux noms des acteurs, je vais limiter les informations retournées en précisant les champs souhaités dans un document JSON (toujours ce fameux JSON). Et, également passer ce document comme deuxième argument de ma recherche find.

```{admonition} Embellissez les résultats de la fonction find ! 
:class: tip

Les résultats de la fonction find() peuvent apparaître désorganisés. MongoDB fournit pretty() qui affiche les résultats sous une forme plus lisible. La synthaxe est la suivante : collectionName.find().pretty() 😉
```

Pour plus de renseignements sur la **fonction find()**, consultez la documentation MongoDB [disponible ici](https://docs.mongodb.com/manual/reference/method/db.collection.find/).

---

## Requêtes plus complexes en utilisant des opérateurs

Les opérateurs se séparent en deux grandes parties : les **opérateurs de comparaison** et les **opérateurs logiques**.

### Opérateurs de comparaison

L'opérateur de comparaison permet de comparer deux élements entre eux. Le tableau suivant l'ensemble des operateurs de comparaison : 

| Opérateur logique 	| Mot clé en MongoDB 	|
|-	|-	|
| = 	| $eq 	|
| < 	| $lt 	|
| > 	| $gt 	|
| ≤ 	| $lte 	|
| ≥ 	| $gte 	|
| ∈ 	| $in 	|
| ∉ 	| $nin 	|
| négation 	| $not 	|
| clé existante 	| $exists 	|
| \|.\| 	| $size 	|

Les opérateurs `$eq`, `$lt`, `$gt`, `$lte`, `$gte` s'ulisent de la même façon en MongoDB. Ces opérateurs comparent la valeur d'une variable à une valeur fixe (nombre, booléen, chaine de caractères...).

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

MongoDB
^^^
```javascript
db.t.find(
    {"a": { $exists: true}
    }
)
```
Cette requête renvera donc les documents ayant le sous-document `a` existant.

Enfin, l'opérateur `$size` permet des récuperer les documents avec des sous-documents d'une certaine taille. Sa syntaxe en MongoDB s'écrit comme suit :

MongoDB
^^^
```javascript
db.t.find(
    {"a": { $size: 5}
    }
)
```

 Le résultat obtenu est l'ensemble des documents avec le sous-document `a` qui est de taille **5**.

### Opérateurs logiques

Les différents opérateurs logiques en MongoDB sont : `and`, `or` et `nor`. Ces opérateurs de tester plusieurs conditions simultanément

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


MongoDB
^^^
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

---

## Méthodes utiles pour des requêtes en MongoDB

### Valeurs distinctes d'un champ : la méthode `distinct`

L'opérateur `distinct` permet ne renvoyer que les valeurs distinctes d'un champ ou d'une liste de conditions. C'est l'équivalent du `DISTINCT` en SQL.

````{panels}

MongoDB
^^^
```javascript
db.nomDeLaCollection.distinct(
    {"b": true}
)
```

---

Notation SQL
^^^
```sql
SELECT DISTINCT(b)
FROM nomDeLaCollection
```

````

La requête ci-dessus permet de renvoyer tous les éléments distincts de `b` de la collection choisie. Si elle est bien formulée, on devrait obtenir tous les valeurs possibles du champ une fois au maximum.

### Connaître le nombre de documents dans une collection : la méthode `count`

La fonction `count` permet de compter le nombre d'éléments ou de documents présents dans une collection. On peut l'utiliser directement sur la collection de base ou bien l'utiliser après avoir exécuter une requête.

````{tabbed} Sur une collection sans requête

```javascript
db.nomDeLaCollection.count()
```
````

````{tabbed} Sur une collection après requête

```javascript
db.nomDeLaCollection.find({"a": 1}).count()
```
````

Bien entendu, les résultats seront différents car on n'a pas le même nombre de documents ou d'éléments avant et après une requête.

### Trier la récupération des documents : la méthode `sort`

### Limiter la récupération des documents : la méthode `limit`
