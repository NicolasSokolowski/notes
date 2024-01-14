# Postgres

Sur une requête SQL de type SELECT, les opérateurs sont appliqués dans un certain ordre.  
Cet ordre est le même pour toutes les requêtes. Le seul élément obligatoire est le `SELECT`.  
Le `FROM` sert à partir du moment où on veut utiliser des données de notre base.

| SQL | Notes |
|:---:|:----- |
| **FROM** | On sélectionne toutes les colonnes et toutes les lignes d'une (ou plusieurs) table |
| **JOIN** [ON] | On ajoute d'autres tables |
| **WHERE** | On commence à retirer les élements ne correspondant pas aux conditions |
| **GROUP BY** | On regroupe nos données sous ensemble |
| **HAVING** | On exclut certain sous ensemble (souvent avec des critères d'agrégat) |
| **SELECT** | On exclut des colonnes et on calcule des valeurs |
| **ORDER BY** | On trie les lignes |
| **LIMIT** | On conserve exclusivement un subset de lignes |

<br/>

---

<details>
<summary>CONSTRAINT + DOMAIN + CHECK</summary>
<br/>

`ADD CONSTRAINT` permet d'ajouter une contrainte.  
Il est possible de donner un nom à la contrainte crée : `ADD CONSTRAINT "<nom_de_la_contrainte>"`

Si on ne précise pas de nom de contrainte, le système va donner un nom par défaut : `<nom de la colonne>_key`

---

Il est possible de standardiser une contrainte en créant un `DOMAIN`. Il s'agit d'une extension d'un type existant auquel on vient ajouter des contraintes supplémentaires.

```sql
CREATE DOMAIN "posint" AS INT (
  CHECK "value" > 0
);
```

`CHECK` : permet de comparer la valeur d'une colonne avec une constante, mais également de comparer plusieurs colonnes entre-elles.

Il est ensuite possible d'utiliser le domaine comme un type de donnée classique :

```sql
ALTER TABLE "package"
  ALTER COLUMN "weight" TYPE posint,
  ALTER COLUMN "height" TYPE posint,
  ALTER COLUMN "worth" TYPE posint,
  ALTER COLUMN "width" TYPE posint,
  ALTER COLUMN "depth" TYPE posint;
```
</details>

---

<details>
<summary>VIEW</summary>
<br/>

`CREATE VIEW` permet de sauvegarder une requête et de s'en servir à nouveau en l'utilisant comme une table.  
```sql
CREATE VIEW "post_with_category" AS
SELECT "post".*, "category"."label" AS "category"
FROM "post"
JOIN "category" ON "category"."id" = "post"."category_id";
```

Il est ensuite possible d'utiliser cette view comme une table classique :
```sql
SELECT * FROM "post_with_category";
```

Cela revient à écrire une sous-requête (une requête imbriquée dans une autre) :  
```sql
SELECT * FROM (
  SELECT "post".*, "category"."label" AS "category"
  FROM "post"
  JOIN "category" ON "category"."id" = "post"."category_id"
) AS "post_with_category";
```

Il n'est pas possible d'UPDATE les données d'une view : elle n'est là que pour être lue.  

`DROP VIEW` permet de supprimer une view :
```sql
DROP VIEW "post_with_category";
```
</details>

---

<details>
<summary>ARRAY_AGG</summary>

`ARRAY_AGG` permet de réduire le nombre de lignes en regroupant les valeurs d'une colonne sous forme de tableau.

```sql
SELECT f.name, f.location, ARRAY_AGG(r.label) AS labels
FROM "variety" AS v
JOIN "row" AS r
	ON v.id = r.variety_id
JOIN "field" AS f
	ON f.id = r.field_id
WHERE v.bitterness = 5
GROUP BY f.name, f.location;
```
</details>

---

<details>
<summary>INDEX</summary>
Les index permettent de trouver une information plus rapidement en organisant les tables.  

<br/>

```sql
CREATE INDEX [<name>] ON <table> (<column>)
```
Créer un index est une action qu'on ne fait qu'une seule fois, sa maintenance est gérée par le SGDB. Par contre, elle peut prendre du temps (de quelques secondes à plusieurs heure dans certains cas).  
En production, la création d'un index se fait généralement de façon concurrente, avec le mot-clé `CONCURRENTLY` &rarr; cela permet à l'indexation de se mettre en pause si la base de donnée est solicitée.

Il existe 2 types d'index les plus populaires :

- `B-tree` : le gendre idéal des index : optimisé pour la comparaison (<, <=, =, >=, >), il est par extension utilisé dans les `ORDER BY`, les `BETWEEN` et même les recherches par motif, quand le motif commence par une partie constante. C'est le type par défaut, pas besoin de le préciser à la création.  
- `Hash` : un index aussi spécifique qu'efficace : imabattable pour l'égalité, il n'est utile que dans ce cas. Si vous n'utilisez n'importe quel autre opérateur que = sur la colonne indexée, l'index sera indexé (mis de côté).

Mais il existe également `BRIN`, qui va avoir un temps d'exécution plus long mais qui réduit considérablement le poids des index.  
[Article sur l'index BRIN pour PGSQL](https://www.percona.com/blog/brin-index-for-postgresql-dont-forget-the-benefits/)
</details>

---

`::` : transtypage
`::interval` : permet de définir un intervalle. *exemple* :  
`'1 day'::interval`


---



