# Fonctions 

## BASE

### Déclaration de la fonction 

```python
def area_of_circle(r):
    pi = 3.14
    result = pi * r * r
    return result
```

- `def`: mot clé qui déclare une nouvelle fonction 
- `area_of_circles`: nom de la fonction 
- `(r)`: paramètre de la fonction 

Après le `:`, on définis le body de la fonction. C'est ce code qui est exécuté à chaque appel de la fonction.

Le `return` permet de retourner une valeur de la fonction.

### Utilisation de la fonction 

Pour appeler la fonction, il faut venir l'appeler par son nom, et passer les arguments si nécessaire 

```python
area = area_of_circle(5) # appel de la fonction
print(area)
# 78.5
```

La variable `area` contient la valeur de retour de la fonction.

