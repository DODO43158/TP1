# TP1 : Comptage de mots avec Python, MapReduce, Hadoop et PySpark

Ce TP compare **quatre méthodes** pour compter le nombre d'occurrences de chaque mot dans un fichier texte, sur des fichiers de plus en plus gros :

1. **Python simple** : lecture du fichier et comptage avec un dictionnaire ;
2. **Pseudo-MapReduce** : reproduction en Python des étapes *map*, *shuffle* et *reduce* ;
3. **Hadoop MapReduce** : job `wordcount` exécuté sur HDFS / YARN ;
4. **PySpark** : `flatMap`, `map` et `reduceByKey` sur un RDD.

## Contenu du dépôt

| Fichier | Description |
|---|---|
| `comptmts.ipynb` | Notebook complet du TP |
| `mots.txt` | Fichier de base : 100 lignes, un mot par ligne (vocabulaire de 15 mots) |
| `resultats.csv` | Temps mesurés pour chaque taille de fichier |
| `courbesparmethode.png` | Une courbe de temps par méthode |
| `courbepyspark.png` | Temps de PySpark selon la taille du fichier |
| `courbecomparaison.png` | Comparaison des méthodes (échelle logarithmique) |

## Démarche

- On génère un fichier de **N = 100 lignes**, puis des fichiers de **m × N lignes** par duplication, avec m allant de 1 à 100 000 (de 100 lignes à **10 millions de lignes**, environ 58 Mo).
- Pour chaque taille, on mesure le temps de chaque méthode (moyenne de 3 exécutions, sauf pour Hadoop).
- Les quatre méthodes donnent exactement les mêmes résultats.

## Principaux résultats (10 millions de lignes)

| Méthode | Temps |
|---|---|
| Python simple | 1,4 s |
| Pseudo-MapReduce | 4,7 s |
| PySpark | 5,4 s |
| Hadoop MapReduce | 21,9 s |

- **Python simple** est le plus rapide à cette échelle.
- **Pseudo-MapReduce** est environ 3 fois plus lent, car il stocke toutes les paires (mot, 1) en mémoire.
- **Hadoop** prend environ 20 s quelle que soit la taille : c'est surtout son **coût de démarrage** (YARN, JVM, HDFS).
- **PySpark** a un coût fixe bien plus faible (environ **0,3 s**) et reste environ **4 fois plus rapide que Hadoop**.

![Comparaison des méthodes](courbecomparaison.png)

**Conclusion :** à cette échelle (au plus 58 Mo sur une seule machine), Python suffit. Hadoop et Spark sont conçus pour de très gros volumes répartis sur plusieurs machines, où leurs coûts fixes deviennent négligeables.

## Exécution

- Python 3 avec `pandas` et `matplotlib`.
- **PySpark** : `pip install pyspark` (Java est nécessaire).
- **Hadoop** : installation locale de Hadoop avec HDFS et YARN démarrés (variable `HADOOP_HOME`). Sans Hadoop, le notebook saute simplement cette méthode.

*Note : le fichier `resultats.csv` du dépôt contient les temps d'une exécution sans PySpark. Les temps de PySpark se trouvent dans le notebook.*
