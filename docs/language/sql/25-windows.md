# Windows Fonctions 

Les fonctions window sont des outils puissant qui permet de bosser sur les données ligne par ligne, tout en gardant le contexte complet.

Elles permettent de faire des calculs (somme, moyenne, range, etc) sur une "fenêtre" de lignes, sans faire de collapse sur les données. Cela veut dire que contrairement au fonctions d'agrégations, on obtiens à la fois le résutat et les détails dans chaque ligne.

```sql
SELECT
    student_id,
    grade,
    RANK() OVER (ORDER BY grade DESC) AS rank
FROM
    students;
```

Avec cette requête, on obtient une table où chaque étudiant a son rang unique selon sa note.

---

## ROW_NUMBER()

Retourne un numéro unique pour chaque ligne dans la window. 

```sql
ROW_NUMBER() OVER ([PARTITION BY colonne] ORDER BY colonne)
```

- `PARTITION BY colonne`: optionnel; divise les données en groupes. Si omit, la numérotation sera globale sur tout le set
- `ORDER BY colonne`: définit l'ordre des lignes pour la numérotation 
