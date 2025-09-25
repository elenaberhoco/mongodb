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

# MongoDB : Base de données NoSQL orientée documents

## Plan du cours

Ce cours est une introduction à MongoDB, une base de données NoSQL orientée documents.

Il est organisé selon les quatre grandes catégories d'interaction que l'on peut avoir avec une base de données, résumées par l'acronyme **CRUD** :
- **C**reate (Créer) : Toute opération qui consiste à ajouter de nouveaux enregistrements à la base de données.
- **R**ead (Lire) : Toute opération qui consiste à récupérer certaines informations en fonction des critères de recherche spécifiés.
- **U**pdate (Mettre à jour) : Toute opération qui consiste à modifier des enregistrements de la base de données.
- **D**elete (Supprimer) : Toute opération qui consiste à supprimer des enregistrements de la base de données, voire la base de données elle-même.

## MongoDB

MongoDB est une **base de données NoSQL orientée documents**.

Les données sont stockées selon un système de paires clé-valeur dans des **documents** semi-structurés.
Contrairement aux **bases de données clé-valeur**, la valeur est consultable : il est possible d'interroger le contenu d'un document et de le modifier---totalement ou partiellement---sans avoir besoin de récupérer 
l'intégralité du document à l'aide de sa clé. Dans MongoDB, les documents sont stockés au format BSON (Binary JSON).

Une **collection** est constituée de documents qui présentent des similarités sur le plan structurel et / ou conceptuel. 
Une base de données peut contenir plusieurs collections.
Les collections sont comparables aux tables dans les **bases de données relationnelles**,
la différence étant que tous les documents d'une collection n'ont pas nécessairement les mêmes champs (le **schéma** est **flexible**). 
Les bases de données orientées documents peuvent également stocker une plus grande variété de données que les bases de données relationnelles.

`````{admonition} Example
:class: tip

La base de données de l'université Prestige contient deux collections : une collection `Étudiants` pour les étudiants de l'université, et une collection `Employés` pour le personnel de l'université. 
Chaque document de la collection `Étudiants` contient des informations sur un étudiant de l'université : nom, prénom, âge, numéro étudiant, formation etc.
Les documents de la collection `Étudiants` peuvent contenir des champs différents, par exemple :

::::{grid}
:gutter: 2

:::{grid-item-card} Étudiant boursier

```javascript
{
    "_id" : ObjectId("56011920de43611b917d773d"),
    "nom" : "Zola",
    "prenom" : "Émile",
    "sexe" : "M",
    "age" : 19,
    "bourse" : "échelon 2"
}
```
:::
:::{grid-item-card} Étudiante en alternance

```javascript
{
    "_id" : ObjectId("65511420al87311b100q246t"),
    "nom" : "Christie",
    "prenom" : "Agatha",
    "sexe" : "F",
    "age" : 21,
    "entreprise" : "Éditions du Masque",
    "contrat" : "alternance"
}
```
:::
::::

Notez qu'il n'y a pas d'attributs (ou *clés*) vides dans MongoDB : les *valeurs* nulles sont remplacées par l'absence des champs correspondant.
Dans l'exemple ci-dessus : l'attribut `bourse` (resp. `entreprise`, `contrat`) n'apparait pas dans le document de l'étudiante en alternance (resp. étudiant boursier).

`````
