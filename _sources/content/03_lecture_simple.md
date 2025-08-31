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

# Requête simple

La méthode `find()` permet de **sélectionner des documents en fonction de critères de filtrage**. Elle renvoie un **curseur**, c'est-à-dire un pointeur vers les résultats d'une requête. Les curseurs permettent d'itérer sur les résultats **un lot à la fois**.    
La syntaxe de la méthode `find()` est la suivante : 

```
db.<collection>.find(<filtre>, <projection>) 
```

````{admonition} Example
:class: tip

Dans la suite, on utilisera la base de données suivante :
```
use Cuisine
db.recettes.insert({
                    "nom":"Ratatouille",
                    "type":"plat principal",
                    "ingredients": ["aubergine", "courgette", "poivron", "tomate", "oignon","ail"],
                    "origine":{"pays":"France","region":"Provence"}
                    })  
db.recettes.insert({
                    "nom":"Tiramisu",
                    "type":"dessert",
                    "ingredients":["oeufs","sucre","mascarpone","boudoirs","café","cacao"],
                    "origine":{"pays":"Italie","region":"Vénétie"},
                    "etapes": [
                        { "numero": 1, "description": "Séparer les blancs des jaunes. Fouetter les jaunes avec le sucre jusqu’à blanchiment." },
                        { "numero": 2, "description": "Incorporer le mascarpone aux jaunes sucrés jusqu’à obtenir une crème lisse." },
                        { "numero": 3, "description": "Monter les blancs en neige et les incorporer délicatement à la crème." },
                        { "numero": 4, "description": "Tremper rapidement les biscuits dans le café froid et tapisser le fond du plat." },
                        { "numero": 5, "description": "Alterner une couche de crème, une couche de biscuits. Terminer par de la crème." },
                        { "numero": 6, "description": "Réfrigérer au moins 4 h. Saupoudrer de cacao avant de servir." } 
                    ]
                    })  
db.recettes.insert({
                    "nom":"Soupe à l'oignon",
                    "type":"entrée",
                    "ingredients": ["oignon","vin","gruyère","beurre"]
                    })  
db.recettes.insert({
                    "nom":"Quiche lorraine",
                    "type":"plat principal",
                    "ingredients":["pâte brisée", "oeufs", "crème fraîche", "lardons", "gruyère"],
                    "origine":{"pays":"France","region":"Lorraine"}
                    })  
db.recettes.insert({
                    "nom":"Mousse au chocolat",
                    "type":"dessert",
                    "ingredients": ["chocolat noir", "oeufs", "sucre"]
                    })  
```
````

## Requête sans condition

Pour **lister l'ensemble des documents de la collection**, il suffit d'utiliser `find()` sans spécifier de conditions.
````{admonition} Example
:class: tip

```
db.recettes.find()  
db.recettes.find({})
```
````
La fonction `findOne()` permet de **ne retourner qu'un seul document**.
````{admonition} Example
:class: tip

```
db.recettes.findOne()   
db.recettes.findOne({})
```
````

## Requête avec condition

### Filtre           

Un **filtre de sélection** peut être passé à `find()` pour ne renvoyer que les documents correspondant à certains critères.  

La syntaxe pour extraire les documents dont l'un des champs vérifie une **condition d'égalité** est la suivante :
```
db.<collection>.find({<key>:<value>})
``` 

````{admonition} Example
:class: tip

La commande suivante permet de retourner toutes les recettes de dessert :

```
db.recettes.find({"type":"dessert"})
```
````
:::{caution}
Cette opération se comporte différemment avec les listes. Elle renvoie les documents pour lesquels la liste **contient** la valeur.
:::
````{admonition} Example
:class: tip

La commande suivante retourne tous les plats contenant des oeufs, c'est-à-dire le tiramisu, la quiche Lorraine et la mousse au chocolat :
```
db.recettes.find({"ingredients":"oeufs"})
```
````

La syntaxe `<embedded_object>.<embedded_key>` permet d'accéder aux attributs d'un **document imbriqué** ou aux valeurs d'une **liste**. Il devient alors possible d'extraire des documents **en fonction du contenu** de ces objets.

````{admonition} Example
:class: tip

Que retourne la commande suivante ? 
```
db.recettes.find({"origine.pays":"France"})
```
Notez que cette commande n'est pas équivalente à :
```
db.recettes.find({"origine":{"pays":"France"}})
```
Dans ce cas, aucun document n'est retourné car aucun document n'a un champ `origine` *exactement* égal à `{"pays":"France"}`. Ils contiennent tous un champ imbriqué additionnel : `region`.   

Que renvoie la commande suivante ?
```
db.recettes.find({"ingredients.1":"oeufs"})
```
````
Cette façon de procéder fonctionne également pour les listes de documents imbriqués. Par exemple :

````{admonition} Example
:class: tip

Pour récupérer tous les documents dont la recette a une troisième étape il suffit de taper :
```
db.recettes.find({"etapes.numero":3})
```
````

### Projection

La plupart du temps, seule une partie de l'information contenue dans les documents nous intéresse. Le second argument de la fonction `find()`, appelé **projection**, permet de **spécifier les clés à renvoyer** dans les documents qui correspondent au filtre de requête. Pour ce faire, vous pouvez :
- soit indiquer les champs que vous souhaitez inclure : `{<key>:<1_or_true>}`  
- soit indiquer les champs que vous souhaitez exclure : `{<key>:<0_or_false>}`   

Vous ne pouvez pas faire les deux à la fois. La seule exception est `_id` que vous pouvez explicitement exclure tout en listant des champs à inclure. L'attribut `_id` est inclus par défaut dans les documents renvoyés.

````{admonition} Example
:class: tip

Pour extraire le nom et le type de tous les plats contenant des oeufs, et exclure le champ `_id` des résultats, il suffit de taper :
```
db.recettes.find({"ingredients":"oeufs"},{"nom":true,"type":true,"_id":false})
```
Pour trouver la région d'origine de la Ratatouille, il suffit de taper :
```
db.recettes.find({"nom":"Ratatouille"},{"origine.region":1})
```
Pour connaître le nom de tous les plats disponibles dans notre collection de recettes, il suffit de ne fixer aucune condition sur les documents :  
```
db.recettes.find({},{"nom":true,"_id":false})
``` 
````

## Modification du curseur

## Exercice

## En résumé

Encore un joli tableau !

Objectif | Syntaxe
--- | ---
Récupérer toutes les occurrences de la collection | `db.collectionName.find({})`
Récupérer la première occurrence de la collection | `db.collectionName.findOne({})`
Filtrer les données | `db.collectionName.find({"x": valeur})`
Limiter l'affichage des clés | `db.collectionName.find({}, {"key": true}) `
Formater les documents de sortie | `db.collectionName.find({}).pretty()`
Trier les documents de sortie | `db.collectionName.find().sort({"key" : 1})`
Compter les documents de sortie | `db.collectionName.find({}).count()`
Limiter les documents de sortie | `db.collectionName.find({}).limit(2)`
Valeurs distinctes d'un champ | `db.collectionName.distinct(champ, {})`
