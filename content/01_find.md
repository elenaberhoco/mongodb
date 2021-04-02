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

# Premières requêtes

* Auteurs/trices : **Julie FRANCOISE, Manon MAHEO et Valentin PENISSON**

Ce chapitre traite des points suivants :
* Syntaxe de requêtes simples (syntaxe de `find`, opérateurs de comparaison, `distinct`, `count`, `sort`, `limit`)

## Interrogation des données et syntaxe de `find`
 
 ```{admonition} Remarque
Toute commande sur la collection restaurants utilise le préfixe : "db.restaurants". Il suffira d’y associer la fonction souhaitée pour avoir un résultat.
```
 Pour filtrer les données, on utilise la fonction find. 
 
 Il existe en mongoDB deux types de requêtes simples, retournant respectivement toutes les occurences d'une collection ou la premiere 
 tous les élements et aucune contrainte
 db.NYfood.find() 
 ne retourne que le premier élément de la liste de résultats
 db.NYfood.findOne()

lorsqu'on ajoute un {} vide entre parenthèses, pas de contrainte.

mais on peut aussi utiliser un document masque, si l'on souhaite fixer des contraintes sur les documents à retourner, pour cela
il suddifit de passer en argument d'une de ces fonctions un document masque contentant les valeurs souhaitées.

Par exemple, la requête suivante retourne tous les documents ayant un champ "x" dont la valeur est "y".

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

## Opérateurs

Les opérateurs se séparent en deux grandes parties : les **opérateurs de comparaison** et les **opérateurs logiques**

### Opérateurs de comparaison

L'opérateur de comparaison, comme son l'indique, compare deux valeurs.



### Opérateurs logiques

Les opérateurs logiques englobent `and`, `or` et `nor`.

#### Le `and` logique

Pour faire une requête avec un `and` logique en MongoDB, il suffit de séparer par une virgule chaque condition : 

MongoDB
^^^
```javascript
db.t.find(
    {"a": 1},
    {"b": 5}
)
```

---

Équivalent SQL
^^^
```sql
SELECT *
FROM t
WHERE a = 1 and b = 5
```

#### Le `or` logique

Le `or` logique se construit de la manière suivane : `$or : [{condition 1}, ... , {condition i}]`. VOici un exemple faisant le parralèle entre le langage MongoDB et le langage SQL :

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
