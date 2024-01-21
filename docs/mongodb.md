# MongoDB

## Installation

`mongod --version` pour voir la version actuelle de `mongoDB`

`tar --version` permet de voir la version de `tar`

Un fichier `tar.gz` est un fichier qui en regroupe plusieurs : lors d'un téléchargement de plusieurs fichiers, le débit max va être plus petit car le poid de chacun des fichiers étant plus petit, le débit va redscendre avant de remonter pour le fichier suivant. L'idée derrière `tar`, c'est donc d'avoir un seul et unique fichier qui pèse plus lourd pour pouvoir avoir un débit max plus important et donc de télécharger plus vite le fichier.  
`tar` permet également la compression de fichier, qui réduit le poids de celui-ci.

`tar zxvf ./data.tar.gz` :
- `z` : zip (compression)
- `x` : extraction
- `v` : verbose 
- `f` : fichier (suivi du chemin du fichier comme dans l'exemple ci-dessus)

En tapant cette commande, on extrait le fichier.

Pour lancer MongoDB : `sudo service mongod start`

Et pour importer un fichier dans une base MongoDB &rarr; `mongoimport --collection=rides --db=oparc --jsonArray --type=json data.json` :  
- `mongoimport` : exécutable fourni à l'installation de MongoDB. Il permet l'import de données dans une collection mongo
- `--collection=<nom de la collection>` : choix de la collection dans laquelle sera importé les données
- `--db=<nom de la base de données>` : choix de la base de données
- `--jsonArray` : spécifie la nature de l'élément qui est importé
- `--type=json` : le type des données reçues
- `data.json` : le nom du fichier pour l'importation

<br/>

La commande `mongosh` permet de se connecter à une base de données test

`show dbs` permet d'afficher toutes les bases données existantes

`use <nom de la db>` pour se connecter à une base de données. Si elle n'existe pas, elle sera créée

`db.dropDatabase()` permet de drop la database courante

MongoDB, lors de l'insertion de données, créer automatiquement une nouvelle propriété `_id` qui correspond à la propriété `id` d'une base de données SQL. Cet id est transmis à une fonction `ObjectId` qui va hash des informations tel que la date de création de la donnée et son id. Cela permet l'unicité de cet id.

---

`npm i mongodb`

```js
const url = 'mongodb://localhost:27017';
const client = new MongoClient(url);

await client.connect();
console.log('Connected successfully to server');

const db = client.db(dbName);
const collection = db.collection('documents');

client.close();
```

---
<details>
<summary>Récupération de données</summary>
<br/>

Une fois connecté à une db :  
`db.<nom de la collection>.find()` pour récupérer toutes les données de la collection &rarr; équivalent du `SELECT * FROM <nom de la table>` en SQL. La recherche est limitée à un certain nombre, il faut taper "it" pour en afficher davantage.

`db.rides.find({"f2":"000000000012d5d7"})` permet de récupérer tous les évèvenements ayant pour valeur celle passée en paramètre.

`db.rides.find({"f1":"la Tour de l'Array", "f2":"000000000012d5d7"})` : la `virgule` ici est considérée comme étant un `ET`.

`db.rides.find({"$or": [{"f1":"la Tour de l'Array"}, {"f2":"000000000012d5d7"}]})` : `$or` permet de rechercher les évèvenements qui ont soit l'un soit l'autre propriété étant égal à la valeur recherchée. Il faut le placer au début !

`db.rides.find({"$or": [{"f1":"la Tour de l'Array"}, {"f2":"000000000012d5d7"}]}, {f3:true})` : on peut spécifier à la fin si l'on veut ou non de certaines données : `{f3:true}` ou `{f3:1}` pour indiquer qu'ont souhaite récupérer la colonne, ou alors ne pas la récupérer avec `{f3:false}` ou `{f3:0}`. La propriété `_id` sera toujours retournée par défaut : il faut spécifier si l'on ne veut pas la récupérer : `{_id:false}`.

</details>

---
<details>
<summary>Insertion de données</summary>
<br/>

`db.rides.insertOne` permet d'insérer une donnée. On passera un objet en paramètre.  
`db.rides.insertMany` permet d'insérer plusieurs données. On passera un tableau d'objets en paramètre.

Si l'on ajoute une nouvelle propriété dans l'objet de notre insertion qui n'existe pas dans les autres jeux de données : Mongo ne fait pas de vérification et ajoute la donnée &rarr; c'est le principe de MongoDB !

</details>

---
<details>
<summary>Mise à jour de données</summary>
<br/>

`db.rides.updateOne({_id: ObjectId("65ad1a67720cc2401a3fec7f")}, {$unset: {f2: true}})` : `{$unset: {f2: true}}` permet de supprimer une propriété

`$set` permet d'ajouter une propriété  
`$inc` permet d'incrémenter  
`{$push: {arr: "val"}}` permet d'ajouter une valeur dans un tableau  
`{$addToSet: {arr: "val"}}` permet d'ajouter une valeur dans un tableau seulement si elle n'est pas déjà existante dans ce tableau.

Pour renommer les propriétés :
`db.rides.updateMany({}, {$rename: {f1: "event", f2: "visitor", f3: "timestamp"}})`


On peut également faire un `upsert` (update + insert) : mis à jour du ou des documents trouvés et si la valeur de la propriété n'existe pas, elle sera créée. Si aucun document n'est trouvé, alors il sera créé :
```js
const upsertResult = await ridesCollection.updateMany({
  timestamp: "2024-01-19 11:10:00.000000+01:00",
}, {
  $inc: {count: 1}, "$setOnInsert": {event: "Le Manoir des vieux clous", timestamp: "2024-01-19 11:10:00.000000+01:00"}}, {upsert:true});
```
Il faut bien penser à spécifier `{upsert:true}` et préciser les propriétés et leur valeur au cas où elles n'existeraient pas avec : `"$setOnInsert": {}`
</details>

---
<details>
<summary>Suppression de données</summary>
<br/>

`db.rides.deleteOne({_id: ObjectId("65ad1a67720cc2401a3fec7f")})` permet de supprimer un jeu de données qui correspond au paramètre passé à la fonction.

`$in` permet de filtrer plusieurs valeurs sur un même champs :
```js
const deleteResult = await ridesCollection.deleteMany({$in:
  {timestamp: ["2024-01-19 11:10:00.000000+01:00", "2024-01-20 11:10:00.000000+01:00"]}
});
```
</details>

---
<details>
<summary>La méthode AGGREGATE</summary>
</br>

La méthode `aggregate` prend en paramètres un tableau de fonctions qui vont être exécutées les unes après les autres. Les fonctions vont réaliser un traitement sur le résultat de la fonction précédente.

La première fonction d'aggrégat est `$match` : elle permet de récupérer uniquement les documents qui remplisse une condition :
```js
{
    $match: {
        event: {$exists: true}
    }
}
```

On peut limiter les propriétés avant de faire l'aggrégation en elle-même avec `$project` :
```js
{ 
    $project: {
        event: "$event",
        timestamp: true, 
        _id: false
    }
}
```

La fonction `$group` correspond au `GROUP BY` en SQL
```js
{ 
    $group: {
      _id: "$event",
      count: {
        $sum: 1
      }
    }
}
```

La fonction `$sort` permet de trier les résultats : 1 pour ordonner de façon croissante et -1 pour ordonner de façon décroissante :
```js
{
    $sort: {
        count: -1
    }
}
```

</details>

---
<details>
<summary>Compass</summary>
</br>

Compass est la GUI de MongoDB.  

[Téléchargement](https://www.mongodb.com/try/download/compass)

Ouvrir un terminal à l'emplacement du fichier téléchargé et taper la commande suivante :
```
sudo apt install ./mongodb-compass_*_amd64.deb
```

&rarr; MongoDB est installé, vous pouvez le lancer en le retrouvant dans la barre de recherche en tapant : MongoDB Compass ou en le lançant depuis un terminal.

[Petite aide pour le téléchargement et l'installation](https://hevodata.com/learn/mongodb-compass-ubuntu/)

</details>

---