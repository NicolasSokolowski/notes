## Postgres

`ADD CONSTRAINT` permet d'ajouter une contrainte.  
Il est possible de donner un nom à la contrainte crée : `ADD CONSTRAINT "<nom_de_la_contrainte>"`

Si on ne précise pas de nom de contrainte, le système va donner un nom par défaut : `<nom de la colonne>_key`

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

<ins>Configuration avancée (développement)</ins>

Se connecter à postgres : `sudo -i -u postgres psql`  
Taper la commande `SHOW hba_file;` pour récupérer le chemin du fichier de configuration.  
Quitter postgres et taper la commande `sudo nano <chemin du fichier de configuration>`  
Modifier la ligne suivante :  
```
local   all             postgres                                peer
```
en
```
local   all             postgres                                trust
```

### Définir un utilisateur par défaut dans les variables d'environnement

Taper la commande `nano ~/.bashrc` ou `code ~/.bashrc` (pour modifier directement dans VSC) et ajouter les lignes suivantes :  
`export PGUSER=postgres`  
`export PGPASSWORD=js4life`

Il est ensuite possible de se connecter à pg avec l'utilisateur postgres en tapant uniquement `psql`.
Il est également possible de créer une base de donnée plus rapidement avec la commande `createdb`, d'en supprimer une avec `dropdb`.

[Liste de toutes les commandes](https://www.postgresql.org/docs/current/reference-client.html)

<br/>