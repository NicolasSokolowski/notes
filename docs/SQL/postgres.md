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
CREATE DOMAIN us_postal_code AS TEXT
CHECK(
   VALUE ~ '^\d{5}$'
OR VALUE ~ '^\d{5}-\d{4}$'
);
```

`CHECK` : permet de comparer la valeur d'une colonne avec une constante, mais également de comparer plusieurs colonnes entre-elles.

Il est ensuite possible d'utiliser le domaine comme un type de donnée classique :

```sql
CREATE TABLE us_snail_addy (
  address_id SERIAL PRIMARY KEY,
  street1 TEXT NOT NULL,
  street2 TEXT,
  street3 TEXT,
  city TEXT NOT NULL,
  postal us_postal_code NOT NULL
);
```
</details>

---

<details>
<summary>VIEW</summary>
<br/>

`CREATE VIEW` permet de sauvegarder une requête et de s'en servir à nouveau en l'utilisant comme une table.  
```sql
CREATE VIEW comedies AS
    SELECT *
    FROM films
    WHERE kind = 'Comedy';
```

Il est ensuite possible d'utiliser cette view comme une table classique :
```sql
SELECT * FROM comedies;
```

Cela revient à écrire une sous-requête (une requête imbriquée dans une autre) :  
```sql
SELECT * FROM (
    SELECT *
    FROM films
    WHERE kind = 'Comedy';
) AS comedies;
```

Il n'est pas possible d'UPDATE les données d'une view : elle n'est là que pour être lue.  

`DROP VIEW` permet de supprimer une view :
```sql
DROP VIEW comedies;
```
</details>

---

<details>
<summary>ARRAY_AGG</summary>
<br/>

`ARRAY_AGG` permet de réduire le nombre de lignes en regroupant les valeurs d'une colonne sous forme de tableau.

```sql
SELECT
    title,
    ARRAY_AGG (first_name || ' ' || last_name) actors
FROM
    film
INNER JOIN film_actor USING (film_id)
INNER JOIN actor USING (actor_id)
GROUP BY
    title
ORDER BY
    title;
```
</details>

---

<details>
<summary>INDEX</summary>
<br/>

Les index permettent de trouver une information plus rapidement en organisant les tables.

```sql
CREATE INDEX [<name>] ON <table> (<column>)
```
Créer un index est une action qu'on ne fait qu'une seule fois, sa maintenance est gérée par le SGDB. Par contre, elle peut prendre du temps (de quelques secondes à plusieurs heure dans certains cas).  
En production, la création d'un index se fait généralement de façon concurrente, avec le mot-clé `CONCURRENTLY` &rarr; cela permet à l'indexation de se mettre en pause si la base de donnée est solicitée.

Les 2 index les plus populaires :

- `B-tree` : le gendre idéal des index : optimisé pour la comparaison (<, <=, =, >=, >), il est par extension utilisé dans les `ORDER BY`, les `BETWEEN` et même les recherches par motif, quand le motif commence par une partie constante. C'est le type par défaut, pas besoin de le préciser à la création.  
- `Hash` : un index aussi spécifique qu'efficace : imabattable pour l'égalité, il n'est utile que dans ce cas. Si vous n'utilisez n'importe quel autre opérateur que = sur la colonne indexée, l'index sera indexé (mis de côté).

Mais il existe également `BRIN`, qui va avoir un temps d'exécution plus long mais qui réduit considérablement le poids des index.  
[Article sur l'index BRIN pour PGSQL](https://www.percona.com/blog/brin-index-for-postgresql-dont-forget-the-benefits/)
</details>

---

<details>
<summary>FONCTIONS UTILISATEUR SQL</summary>
<br/>

<details>
<summary>CREATION D'UNE FONCTION</summary>
<br/>

```sql
CREATE OR REPLACE FUNCTION <function name> (<param1>, ...) 
```
La signature d'une fonction est déterminé par son nom, le nombre de paramètres ainsi que leur type. Si la signature est différente, la commande va créer une fonction, mais si la signature est identique, il va remplacer l'existante.

On doit toujours définir un type de retour dans une fonction avec le mot clé `RETURNS`.  
Dans le cas où l'on modifie le retour d'une fonction, on doit impérativement `DROP` la fonction avant de la recréer. `REPLACE` ne suffira pas.

```sql
CREATE FUNCTION dup(in int, out f1 int, out f2 text)
    AS $$ SELECT $1, CAST($1 AS text) || ' is text' $$
    LANGUAGE SQL;
```
Dans l'exemple ci-dessus, on peut voir qu'il est possible de nommer les paramètres.  
Les instructions sont données entre simple guillement ou entre `$$` pour pouvoir faire un retour à la ligne.  
En fin de création d'une fonction, on peut choisir le langage de l'instruction de celle-ci. PostreSQL inclut langages procéduraux : `PL/pgSQL`, `PL/Tcl`, `PL/Perl`, and `PL/Python`. Il en existe davantage.

Une fonction doit **TOUJOURS** retourner une valeur. Il n'y a pas de verbe `RETURN` en SQL, on utilisera un `SELECT` ou un `RETURNING` dans le cas d'un `INSERT`.

Une fonction utilisateur n'est disponible que dans la table dans laquelle elle a été créée !
</details>

---
<details>
<summary>APPEL D'UNE FONCTION</summary>
<br/>

Pour appeler une fonction en SQL, on utilise `SELECT` + le nom de la fonction et le choix du ou des paramètres :
```sql
SELECT * FROM dup(42);
```
</details>

---
<details>
<summary>TYPE D'ATTRIBUT D'UNE FONCTION</summary>
</br>

Il existe 3 types d'attributs pour une fonction :
- `VOLATILE`
- `IMMUTABLE`
- `STABLE`

<details>
<summary>VOLATILE</summary>
</br>

Une fonction est dite volatile lorsque le résultat n'est pas prévisible et qu'elle est exécutée à chaque appel. Il s'agit du type d'attribut par défaut et il n'est pas nécessaire de le spécifier.
</details>

---
<details>
<summary>IMMUTABLE</summary>
<br/>

```sql
CREATE FUNCTION factorielle (a integer) RETURNS bigint AS
$$
DECLARE
  result bigint;
BEGIN
  IF a=1 THEN
    RETURN 1;
  ELSE
    RETURN a*(factorielle(a-1));
  END IF;
END;
$$
LANGUAGE PLPGSQL IMMUTABLE;
```
Certaines fonctions que l’on écrit sont déterministes. C’est-à-dire qu’à paramètre(s) identique(s), le résultat est identique.  
Le résultat de telles fonctions est alors remplaçable par son résultat avant même de commencer à planifier la requête.
</details>

---
<details>
<summary>STABLE</summary>
<br/>

Une fonction de ce type va être à l'écoute des modifications de la base de données : en cas d'update, elle vide son cache.
</details>
</details>

---

`RETURNS SETOF` permet de récupérer plusieurs enregistrements.

---

STRICT

Les fonctions définies comme `STRICT` ou `RETURNS NULL ON NULL INPUT` annule l’exécution de la requête si l’un des paramètres passés est `NULL`. Dans ce cas, la fonction est considérée comme ayant renvoyé `NULL`.

</details>

---

`::` : transtypage
`::interval` : permet de définir un intervalle. *exemple* :  
`'1 day'::interval`


---



