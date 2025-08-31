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

## Opérateurs de comparaison

| Opérateur logique     | Mot clé en MongoDB    |
|-  |-  |
| =     | `$eq`     |
| ≠     | `$ne`     |
| <     | `$lt`     |
| >     | `$gt`     |
| ≤     | `$lte`    |
| ≥     | `$gte`    |
| ∈     | `$in`     |
| ∉     | `$nin`    |
| clé existante     | `$exists`     |
| \|.\|     | `$size`   |


## Opérateurs logique

## Requête sur des listes



```{admonition} Remarque

Remarque !
```

### Titre 1.2.1

Text text text

::::{grid}
:gutter: 2

:::{grid-item-card} Retourner toutes les occurences d'une collection avec find()
BLI ?
:::

:::{grid-item-card} Retourner uniquement la première occurence d'une collection avec findOne()
BLO ?
:::
::::

Text

```
code
```
Text

````{tab-set}
```{tab-item} Onglet 1
Blu
```
```{tab-item} Onglet 2
Blo
```
````
Text Text

`````{tab-set}
````{tab-item} Tab 1 title
My first tab
````

````{tab-item} Tab 2 title
```python
def main():
    return
```
bli
````
`````
MORE TABS !!

````{tab} Python
```python
def main():
    return
```
````
````{tab} C++
```c++
int main(const int argc, const char **argv) {
  return 0;
}
```
````

```{admonition} On peut mettre ce qu'on veut !
:class: tip

Et tout plein de texte là
```
Encore plus d'onglets :

::::{tab-set}
:sync-group: category

:::{tab-item} Label1
:sync: key1

Content 1
:::

:::{tab-item} Label2
:sync: key2

Content 2
:::

::::

::::{tab-set}
:sync-group: category

:::{tab-item} Label1
:sync: key1

Content 1
:::

:::{tab-item} Label2
:sync: key2

Content 2
:::

::::


