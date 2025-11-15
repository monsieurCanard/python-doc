![Image](https://github.com/user-attachments/assets/a70caa94-dfa1-4d43-bdd7-b734e684146a)

# Mon guide perso pour Python

## Table des matières
- [Opérateurs](#opérateurs)
  - [Puissances](#puissances)
- [Chaînes de caractères (Strings)](#chaînes-de-caractères-strings)
  - [Multi-lignes](#multi-lignes)
  - [Slicing (Tranches)](#slicing-tranches)
  - [Méthodes de string](#méthodes-de-string)
- [Instructions de contrôle](#instructions-de-contrôle)
  - [pass](#pass)
  - [match (Pattern Matching)](#match-pattern-matching)
- [Fonctions](#fonctions)
  - [Valeur par défaut des arguments](#valeur-par-défaut-des-arguments)
  - [Fonctions anonymes (lambda)](#fonctions-anonymes-lambda)
- [Classes en Python](#classes-en-python)
  - [Variables de classe vs variables d'instance](#variables-de-classe-vs-variables-dinstance)
  - [Héritage des classes](#héritage-des-classes)
  - [Structures avec dataclass](#structures-avec-dataclass)
- [Attributs spéciaux des classes](#attributs-spéciaux-des-classes)
- [Itérateurs et générateurs](#itérateurs-et-générateurs)
- [Fichiers](#fichiers)
  - [Lecture de fichiers](#lecture-de-fichiers)
  - [Écriture de fichiers](#écriture-de-fichiers)
- [Gestion des arguments](#gestion-des-arguments)
- [Tests et documentation](#tests-et-documentation)
- [Gestion des paquets avec pip](#gestion-des-paquets-avec-pip)
- [Gestion des exceptions](#gestion-des-exceptions)
- [Cython](#cython)

---

## Opérateurs

### Puissances

En Python, il est possible de calculer des puissances avec l'opérateur `**` :
```python
5 ** 2  # 5 squared
25
2 ** 7  # 2 to the power of 7
128
```

**Notes**  
Puisque `**` est prioritaire sur `-`, `-3 ** 2` est interprété `-(3 ** 2)` et vaut donc `-9`. Pour éviter cela et obtenir `9`, utilisez des parenthèses : `(-3) ** 2`.

---

## Chaînes de caractères (Strings)

### Multi-lignes

Les chaînes de caractères peuvent s'étendre sur plusieurs lignes. Une façon de faire est d'utiliser les triple-quotes : `"""..."""` ou `'''...'''`. Les caractères de fin de ligne sont automatiquement inclus dans la chaîne, mais il est possible de l'éviter en ajoutant un `\` à la fin de la ligne. Dans l'exemple suivant, le saut de ligne initial n'est pas inclus :

``` python
print("""\
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
""")
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
```

### Slicing (Tranches)

Pour mémoriser la façon dont les tranches fonctionnent, vous pouvez imaginer que les indices pointent entre les caractères, le côté gauche du premier caractère ayant la position 0. Le côté droit du dernier caractère d'une chaîne de n caractères a alors pour indice n. Par exemple :

``` python
 +---+---+---+---+---+---+
 | P | y | t | h | o | n |
 +---+---+---+---+---+---+
 0   1   2   3   4   5   6
-6  -5  -4  -3  -2  -1
```

s[1:4] renvoie les caractères de l'indice 1 à l'indice 4 (exclu), soit 'yth'.

Les valeurs par défaut des indices de tranches ont une utilité ; le premier indice vaut zéro par défaut (c.-à-d. lorsqu'il est omis), le deuxième correspond par défaut à la taille de la chaîne de caractères. Ainsi, s[:2] renvoie les deux premiers caractères, tandis que s[2:] donne tous les caractères à partir de l'indice 2 jusqu'à la fin.

### Méthodes de string 

**str.casefold()**  Renvoie une copie casefolded de la chaîne. Les chaînes casefolded peuvent être utilisées dans des comparaisons insensibles à la casse.

**str.count(sub[, start[, end]])**	Renvoie le nombre de occurrences de la sous-chaîne sub dans la chaîne. Les arguments facultatifs start et end sont des indices qui limitent la recherche à une sous-partie spécifique de la chaîne.
```python
'spam, spam, spam'.count('spam')
3
'spam, spam, spam'.count('spam', 5)
2
'spam, spam, spam'.count('spam', 5, 10)
1
'spam, spam, spam'.count('eggs')
0
'spam, spam, spam'.count('')
17
```

**str.find(sub[, start[, end]])**	Return the lowest index in the string where substring sub is found within the slice s[start:end]. Optional arguments start and end are interpreted as in slice notation. Return -1 if sub is not found. For example:

```python
'spam, spam, spam'.find('sp')
0
'spam, spam, spam'.find('sp', 5)
6
```

Note la méthode find() ne doit être utilisée que si vous avez besoin de connaître la position de sub. Pour vérifier si sub est une sous chaine ou non, utilisez l'opérateur in :
```python
'Py' in 'Python'
True
```

---

## Instructions de contrôle

### pass

L'instruction **pass** est utilisée lorsque une instruction est requise syntaxiquement mais que vous ne voulez pas exécuter de code. Par exemple, vous pouvez utiliser pass comme un espace réservé dans une fonction ou une boucle que vous n'avez pas encore implémentée :
```python
def ma_fonction():
    pass  # À implémenter plus tard
```

### match (Pattern Matching)

L'instruction **match** introduite en Python 3.10 permet de faire du pattern matching, similaire au switch/case dans d'autres langages. Voici un exemple simple :
```python
def http_status(status):
    match status:
        case 200 | 201 | 202:
            return "OK"
        case 404:
            return "Not Found"
        case 500:
            return "Internal Server Error"
        case _:
            return "Unknown Status"
```

Mais on peut egalement faire des correspondances plus complexes avec des structures de données :
```python
# point est un tuple (x, y)
def traiter_point(point):
    match point:
        case (0, 0):
            return "Origine"
        case (x, 0):
            return f"Sur l'axe X à {x}"
        case (0, y):
            return f"Sur l'axe Y à {y}"
        case (x, y) if x == y:
            return f"Sur la ligne y=x à ({x}, {y})"
        case (x, y):
            return f"Ailleurs à ({x}, {y})"
```

Si vous structurez vos données par l'utilisation de classes, vous pouvez former des filtres avec le nom de la classe suivi d'une liste d'arguments. Ces filtres sont semblables à l'appel d'un constructeur, et permettent de capturer des attributs :

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

def where_is(point):
    match point:
        case Point(x=0, y=0):
            print("Origin")
        case Point(x=0, y=y):
            print(f"Y={y}")
        case Point(x=x, y=0):
            print(f"X={x}")
        case Point():
            print("Somewhere else")
        case _:
            print("Not a point")
```

#### Correspondance avec des constantes

Les filtres peuvent contenir des noms qui se réfèrent à des constantes. Ces noms doivent impérativement être qualifiés (contenir un point) pour ne pas être interprétés comme des variables de capture :

```python
from enum import Enum
class Color(Enum):
    RED = 'red'
    GREEN = 'green'
    BLUE = 'blue'

color = Color(input("Enter your choice of 'red', 'blue' or 'green': "))

match color:
    case Color.RED:
        print("I see red!")
    case Color.GREEN:
        print("Grass is green")
    case Color.BLUE:
        print("I'm feeling the blues :(")
```

---

## Fonctions

### Valeur par défaut des arguments

La forme la plus utile consiste à indiquer une valeur par défaut pour certains arguments. Ceci crée une fonction qui peut être appelée avec moins d'arguments que ceux présents dans sa définition. Par exemple :

```python
def ask_ok(prompt, retries=4, reminder='Please try again!'):
    while True:
        reply = input(prompt)
        if reply in {'y', 'ye', 'yes'}:
            return True
        if reply in {'n', 'no', 'nop', 'nope'}:
            return False
        retries = retries - 1
        if retries < 0:
            raise ValueError('invalid user response')
        print(reminder)
```

### Fonctions anonymes (lambda)

Avec le mot-clé `lambda`, vous pouvez créer de petites fonctions anonymes. En voici une qui renvoie la somme de ses deux arguments : `lambda a, b: a+b`. 

Les fonctions lambda peuvent être utilisées partout où un objet fonction est attendu. Elles sont syntaxiquement restreintes à une seule expression. Sémantiquement, elles ne sont que du sucre syntaxique pour une définition de fonction normale. Comme les fonctions imbriquées, les fonctions lambda peuvent référencer des variables de la portée englobante :

``` python
def make_incrementor(n):
    return lambda x: x + n

f = make_incrementor(42)
f(0)
42
f(1)
43
```

L'exemple ci-dessus utilise une expression lambda pour renvoyer une fonction. Une autre utilisation consiste à passer une petite fonction comme argument. Par exemple, `list.sort()` accepte une fonction de tri `key` qui peut être une fonction lambda :

```python
pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
pairs.sort(key=lambda pair: pair[1])
pairs
[(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]
```

---

## Classes en Python

### Variables de classe vs variables d'instance

En général, les variables d'instance stockent des informations relatives à chaque instance alors que les variables de classe servent à stocker les attributs et méthodes communes à toutes les instances de la classe :

``` python
class Dog:

    kind = 'canine'         # class variable shared by all instances

    def __init__(self, name):
        self.name = name    # instance variable unique to each instance

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.kind                  # shared by all dogs
'canine'
>>> e.kind                  # shared by all dogs
'canine'
>>> d.name                  # unique to d
'Fido'
>>> e.name                  # unique to e
'Buddy'
```
``` python
class Dog:

    tricks = []             # mistaken use of a class variable

    def __init__(self, name):
        self.name = name

    def add_trick(self, trick):
        self.tricks.append(trick)

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks                # unexpectedly shared by all dogs
['roll over', 'play dead']
```

**Conception correcte** : Une conception correcte de la classe est d'utiliser une variable d'instance à la place :


``` python
class Dog:

    def __init__(self, name):
        self.name = name
        self.tricks = []    # creates a new empty list for each dog

    def add_trick(self, trick):
        self.tricks.append(trick)

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks
['roll over']
>>> e.tricks
['play dead']
```

### Héritage des classes

Syntaxe de base pour l'héritage :

```python
class DerivedClassName(BaseClassName):
    <statement-1>
    .
    .
    .
    <statement-N>
```

#### Fonctions pour tester l'héritage

Python définit deux fonctions primitives pour gérer l'héritage :

utilisez isinstance() pour tester le type d'une instance : isinstance(obj, int) renvoie True seulement si obj.__class__ est égal à int ou à une autre classe dérivée de int ;

- utilisez `issubclass()` pour tester l'héritage d'une classe : `issubclass(bool, int)` renvoie `True` car la classe `bool` est une sous-classe de `int`. Cependant, `issubclass(float, int)` renvoie `False` car `float` n'est pas une sous-classe de `int`.

### Structures avec dataclass

Une structure est une collection de variables sous un même nom. En Python, les structures peuvent être créées facilement en utilisant le décorateur `@dataclass` :

``` python
from dataclasses import dataclass
@dataclass
class Point:
    x: float
    y: float

point = Point(1.0, 2.0)
print(point.x)  # Affiche 1.0
print(point.y)  # Affiche 2.0
```

---

## Itérateurs et générateurs

``` python
for element in [1, 2, 3]:
    print(element)
for element in (1, 2, 3):
    print(element)
for key in {'one':1, 'two':2}:
    print(key)
for char in "123":
    print(char)
for line in open("myfile.txt"):
    print(line, end='')
```

### Fonctionnement interne des itérateurs

Un itérateur introduit également une méthode `__iter__()` qui renvoie l'itérateur lui-même et une méthode `__next__()` qui renvoie le prochain élément de la séquence. Lorsqu'il n'y a plus d'éléments à renvoyer, `__next__()` lève l'exception `StopIteration` pour signaler la fin de l'itération.

### Expressions génératrices
``` python
sum(i*i for i in range(10))                 # sum of squares
285

xvec = [10, 20, 30]
yvec = [7, 5, 3]
sum(x*y for x,y in zip(xvec, yvec))         # dot product
260

unique_words = set(word for line in page  for word in line.split())

valedictorian = max((student.gpa, student.name) for student in graduates)

data = 'golf'
list(data[i] for i in range(len(data)-1, -1, -1))
['f', 'l', 'o', 'g']
```

---

## Fichiers

``` python
with open('myfile.txt', 'r') as f:
    read_data = f.read()
```

**Modes d'ouverture** :
- `'r'` : lecture (read)
- `'w'` : écriture (write) - écrase le fichier existant
- `'a'` : ajout (append) - ajoute à la fin du fichier
- `'r+'` : lecture et écriture

### Lecture de fichiers

``` python
f.read()       # lit tout le fichier
f.read(100)    # lit les 100 premiers octets
f.readline()   # lit le fichier ligne par ligne 
f.readlines()  # lit toutes les lignes et les renvoie sous forme de liste
```

### Écriture de fichiers

```python
f.write("Hello, World!\n")  # écrit une chaîne dans le fichier
f.writelines(["Line 1\n", "Line 2\n", "Line 3\n"])  # écrit une liste de chaînes dans le fichier
```

---

## Gestion des arguments 

``` python
import sys
print("Nombre d'arguments :", len(sys.argv), "arguments.")
print("Arguments :", str(sys.argv))
print("Nom du script :", sys.argv[0])
```

### Utilisation d'argparse

Pour une gestion plus avancée des arguments en ligne de commande, on peut utiliser le module `argparse` :

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument("--count", type=int, default=1)
parser.add_argument("--verbose", action="store_true")

args = parser.parse_args()

print("count =", args.count)
print("verbose =", args.verbose)
```

**Exemple d'exécution** :
```bash
$ python my_program.py --count 3 --verbose
```

**Résultat** :
```bash
count = 3
verbose = True
```

**Notes importantes** :
- Les arguments avec `--` sont optionnels et peuvent être placés n'importe où dans la ligne de commande.
- Les arguments positionnels (sans `--`) sont obligatoires et doivent être placés dans l'ordre (en tenant compte que les arguments optionnels peuvent être placés n'importe où).

---

## Tests et documentation

Une approche possible pour développer des applications de très bonne qualité est d'écrire des tests pour chaque fonction au fur et à mesure de son développement, puis d'exécuter ces tests fréquemment lors du processus de développement.

### Module doctest

Le module `doctest` cherche des tests dans les chaînes de documentation. Un test ressemble à un simple copier-coller d'un appel et son résultat depuis le mode interactif. Cela améliore la documentation en fournissant des exemples tout en prouvant qu'ils sont justes :
``` python
def average(values):
    """Computes the arithmetic mean of a list of numbers.

    >>> print(average([20, 30, 70]))
    40.0
    """
    return sum(values) / len(values)

import doctest
doctest.testmod()   # automatically validate the embedded tests
```

### Module unittest

Le module `unittest` requiert plus d'efforts que le module `doctest` mais il permet de construire un jeu de tests plus complet que l'on fait évoluer dans un fichier séparé :

``` python
import unittest

class TestStatisticalFunctions(unittest.TestCase):

    def test_average(self):
        self.assertEqual(average([20, 30, 70]), 40.0)
        self.assertEqual(round(average([1, 5, 7]), 1), 4.3)
        with self.assertRaises(ZeroDivisionError):
            average([])
        with self.assertRaises(TypeError):
            average(20, 30, 70)

unittest.main()  # Calling from the command line invokes all tests
```

---

## Gestion des paquets avec pip

### Installer un paquet

Vous pouvez installer la dernière version d'un paquet en indiquant son nom :
```bash
python -m pip install novas
Collecting novas
  Downloading novas-3.1.1.3.tar.gz (136kB)
Installing collected packages: novas
  Running setup.py install for novas
Successfully installed novas-3.1.1.3
```

### Installer une version spécifique

Vous pouvez installer une version spécifique d'un paquet en donnant le nom du paquet suivi de `==` et du numéro de version souhaitée :
```bash
python -m pip install requests==2.6.0
Collecting requests==2.6.0
  Using cached requests-2.6.0-py2.py3-none-any.whl
Installing collected packages: requests
Successfully installed requests-2.6.0
```

### Mettre à jour un paquet

Si vous relancez cette commande, pip remarque que la version demandée est déjà installée et ne fait rien. Vous pouvez fournir un numéro de version différent pour récupérer cette version ou lancer `python -m pip install --upgrade` pour mettre à jour le paquet à la dernière version :
```bash
python -m pip install --upgrade requests
Collecting requests
Installing collected packages: requests
  Found existing installation: requests 2.6.0
    Uninstalling requests-2.6.0:
      Successfully uninstalled requests-2.6.0
Successfully installed requests-2.7.0
```

### Autres commandes utiles

**Désinstaller un paquet** :
```bash
python -m pip uninstall nom_paquet
```

**Afficher les informations d'un paquet** :
```bash
python -m pip show requests
---
Metadata-Version: 2.0
Name: requests
Version: 2.7.0
Summary: Python HTTP for Humans.
Home-page: http://python-requests.org
Author: Kenneth Reitz
Author-email: me@kennethreitz.com
License: Apache 2.0
Location: /Users/akuchling/envs/tutorial-env/lib/python3.4/site-packages
Requires:
```

**Lister tous les paquets installés** :
```bash
python -m pip list
novas (3.1.1.3)
numpy (1.9.2)
pip (7.0.3)
requests (2.7.0)
setuptools (16.0)
```

### Gestion des dépendances avec requirements.txt

`python -m pip freeze` produit une liste similaire des paquets installés mais l'affichage adopte un format que `pip install` peut lire. La convention habituelle est de mettre cette liste dans un fichier `requirements.txt` :
```bash
python -m pip freeze > requirements.txt
 cat requirements.txt
novas==3.1.1.3
numpy==1.9.2
requests==2.7.0
```

Le fichier `requirements.txt` peut alors être ajouté dans un système de gestion de versions comme faisant partie de votre application. Les utilisateurs peuvent alors installer tous les paquets nécessaires à l'application avec `install -r` :
```bash
python -m pip install -r requirements.txt
Collecting novas==3.1.1.3 (from -r requirements.txt (line 1))
  ...
Collecting numpy==1.9.2 (from -r requirements.txt (line 2))
  ...
Collecting requests==2.7.0 (from -r requirements.txt (line 3))
  ...
Installing collected packages: novas, numpy, requests
  Running setup.py install for novas
Successfully installed novas-3.1.1.3 numpy-1.9.2 requests-2.7.0
```

---

## Gestion des exceptions

``` python
def divide(x, y):
    try:
        result = x / y
    except ZeroDivisionError:
        print("division by zero!")
    else:
        print("result is", result)
    finally:
        print("executing finally clause")

divide(2, 1)
result is 2.0
executing finally clause
divide(2, 0)
division by zero!
executing finally clause
divide("2", "1")
executing finally clause
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    divide("2", "1")
    ~~~~~~^^^^^^^^^^
  File "<stdin>", line 3, in divide
    result = x / y
             ~~^~~
TypeError: unsupported operand type(s) for /: 'str' and 'str'
```

### Action de nettoyage avec `with`

Le problème avec certains codes est qu'ils laissent des ressources (comme des fichiers) ouvertes pendant une durée indéterminée après que le code a fini de s'exécuter. Ce n'est pas un problème avec des scripts simples, mais peut l'être au sein d'applications plus conséquentes. 

L'instruction `with` permet d'utiliser certains objets (comme des fichiers) d'une façon qui assure qu'ils seront toujours nettoyés rapidement et correctement :

```python 

with open("myfile.txt") as f:
    for line in f:
        print(line, end="")
```

Après l'exécution du bloc, le fichier `f` est toujours fermé, même si un problème est survenu pendant l'exécution de ces lignes. D'autres objets qui, comme pour les fichiers, fournissent des actions de nettoyage prédéfinies l'indiquent dans leur documentation.

---

## Attributs spéciaux des classes

### 1. `__init__` — Constructeur

Méthode appelée automatiquement lors de la création d'une instance. Permet d'initialiser les attributs de l'objet.

``` python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
```

### 2. `__repr__` — Représentation debug

Renvoie une représentation "technique" de l'objet, utilisée par `repr()` et dans la console interactive. Doit idéalement permettre de recréer l'objet.

```python
class Point:
    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"
```

### 3. `__str__` — Représentation utilisateur

Renvoie une représentation "lisible" de l'objet, utilisée par `str()` et `print()`. Destinée à l'affichage pour l'utilisateur final.

```python
class User:
    def __str__(self):
        return f"User: {self.name}"
```

### 4. `__len__` — Support de len(obj)

Permet d'utiliser la fonction `len()` sur votre objet. Doit renvoyer un entier représentant la "taille" de l'objet.

```python
class Bag:
    def __init__(self, items):
        self.items = items

    def __len__(self):
        return len(self.items)
```

### 5. `__getitem__` — Accès par index obj[index]

Permet d'accéder aux éléments de l'objet avec la syntaxe `obj[index]`, comme pour les listes ou dictionnaires.

```python
class Vector:
    def __init__(self, data):
        self.data = data

    def __getitem__(self, index):
        return self.data[index]
```

### 6. `__setitem__` — Affectation obj[index] = value

Permet de modifier des éléments de l'objet avec la syntaxe `obj[index] = value`, comme pour les listes ou dictionnaires.

```python
class Vector:
    def __setitem__(self, index, value):
        self.data[index] = value
```

### 7. `__contains__` — Opérateur in

Permet d'utiliser l'opérateur `in` pour vérifier si un élément est présent dans l'objet.

```python
class Container:
    def __contains__(self, value):
        return value == 42

42 in Container()   # True
```

### 8. `__iter__` + `__next__` — Itération

Permettent de rendre un objet itérable (utilisable dans une boucle `for`). `__iter__()` renvoie l'itérateur, `__next__()` renvoie l'élément suivant.

```python
class Counter:
    def __init__(self, limit):
        self.i = 0
        self.limit = limit

    def __iter__(self):
        return self

    def __next__(self):
        if self.i >= self.limit:
            raise StopIteration
        self.i += 1
        return self.i
```

### 9. `__eq__` — Comparaison ==

Définit le comportement de l'opérateur d'égalité `==`. Permet de comparer deux objets selon vos propres critères.

```python
class Point:
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y
```

### 10. `__enter__` / `__exit__` — Contexte (with)

Permettent d'utiliser l'objet dans un gestionnaire de contexte `with`. `__enter__()` est appelé au début, `__exit__()` à la fin (même en cas d'erreur).

```python
class FileLogger:
    def __enter__(self):
        self.f = open("log.txt", "w")
        return self.f

    def __exit__(self, exc_type, exc, tb):
        self.f.close()
```

### 11. `__call__` — Rendre un objet appelable

Permet d'appeler un objet comme une fonction. L'objet devient "callable" et peut être utilisé avec des parenthèses `obj()`.

```python
class Adder:
    def __init__(self, value):
        self.value = value

    def __call__(self, x):
        return x + self.value

add5 = Adder(5)
add5(10)  # 15
```

### 12. `__match_args__` — Pattern matching positionnel

Définit l'ordre des attributs à utiliser dans les motifs positionnels du pattern matching. Permet d'écrire `Point(0, y)` au lieu de `Point(x=0, y=y)`.

```python
class Point:
    __match_args__ = ("x", "y")
    def __init__(self, x, y):
        self.x = x
        self.y = y

match p:
    case Point(0, y):
        print("Axe Y :", y)
```

### 13. `__slots__` — Limitation des attributs

Limite les attributs autorisés sur une instance et optimise la mémoire. Seuls les attributs listés peuvent être créés, empêchant l'ajout d'attributs dynamiques.

```python
class User:
    __slots__ = ("name", "age")

u = User()
u.name = "Bob"       # OK
u.address = "Paris"  # Erreur
```

### 14. `__getattr__` — Attribut manquant

Appelé automatiquement quand on accède à un attribut qui n'existe pas. Permet de définir un comportement par défaut pour les attributs inexistants.

```python
class Dummy:
    def __getattr__(self, name):
        return f"{name} n'existe pas"
```

### 15. `__dict__` — Attributs de l'objet

Dictionnaire contenant tous les attributs d'une instance. Permet d'accéder et de modifier dynamiquement les attributs de l'objet.

```python
class A:
    def __init__(self):
        self.x = 10
        self.y = 20

a = A()
print(a.__dict__)  # {'x': 10, 'y': 20}
```

---

## Cython

Cython est un langage de programmation qui facilite l'écriture de modules C pour Python. Il est conçu pour être un sur-ensemble de Python, ce qui signifie que tout code Python valide est également du code Cython valide. Cependant, Cython permet également d'ajouter des annotations de type statique et d'utiliser des fonctionnalités spécifiques au C pour améliorer les performances.

Voici un exemple simple de code Cython qui définit une fonction pour calculer la somme des carrés d'une liste de nombres :

```cython
def sum_of_squares(double[:] arr):
    cdef Py_ssize_t i, n = arr.shape[0]
    cdef double total = 0
    for i in range(n):
        total += arr[i] * arr[i]
    return total
```

### Méthode classique : compiler un fichier .pyx en extension C

#### Étape 1 : Installer Cython
```bash
pip install cython
```

#### Étape 2 : Créer un fichier .pyx

Exemple : `mon_module.pyx`
```cython
cdef int square(int x):
    return x * x

def py_square(int x):
    return square(x)
```

**Notes** :
- `cdef` → fonction / variable C interne
- `def` → fonction Python accessible depuis Python pur

#### Étape 3 : Créer un setup.py pour compiler

```python
from setuptools import setup
from Cython.Build import cythonize

setup(
    ext_modules = cythonize("mon_module.pyx")
)
```

#### Étape 4 : Compiler
```bash
python setup.py build_ext --inplace
```

Cela génère un fichier :
- `.so` (Linux/Mac) 
- `.pyd` (Windows)

C'est une extension Python native compilée en C.

#### Étape 5 : Utiliser dans Python

```python
import mon_module

print(mon_module.py_square(10))  # 100
```
