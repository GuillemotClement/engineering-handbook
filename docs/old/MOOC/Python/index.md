## input 

```python 
name = input("What is your name? ")
print("Hi there, " + name)
```

`name` récupère la valeur saisis par l'utilisateur dans la commande.

---

Pour l'affichage d'une str + une int, il sera nécessaire de convertir le number 
```python 
result = 10 * 25
print("The result is " + str(result))
```
Ou bien d'utiliser une `,`
```python 
result = 10 * 25
print("The result is", result)
```

---

`f-string`permet d'afficher directement la valeur d'une variable dans une string 

```python 
result = 10 * 25
print(f"The result is {result}")
```