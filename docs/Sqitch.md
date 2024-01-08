# Sqitch

Sqitch est un module de versionning pour les migrations de base de données. Il est indépendant du code et n'a besoin que de la base de données.

## <ins>Installation et configuration</ins>

`sqitch --version` pour vérifier la version  
`sqitch init <nom_du_projet> --engine pg --top-dir ./migrations` : initialise sqitch avec pour projet XXXX et pour moteur de base de données postgres, ainsi qu'une création des dossiers et fichiers dans le dossier indiqué.

`sqitch config --user user.name 'Marge N. O'Vera'`  
`sqitch config --user user.email 'marge@example.com'`

Pour vérifier les configs :

`cat ~/.sqitch/sqitch.conf`

Pour ajouter une étape de migration :

`sqitch add <nom_de_la_migration> -n "description de la migration"`

Pour déployer le script, il faut taper la commande :

`sqitch deploy db:pg:<database_name>`

`sqitch status` permet de vérifier l'état de la base de données

Pour revert (revenir en arrière) :

`sqitch revert db:pg:<database_name>`

**Note** pour la partie vérify de la migration :
On ne veut pas récupérer d'enregistrements mais simplement vérifier l'existence d'une table. Pour cela on va ajouter la condition `WHERE false;` qui est toujours invalide.

`sqitch deploy db:pg:<database_name> --verify` : si une incohérence est détectée, le déploiement est annulé.

`sqitch revert db:pg:<database_name> --to <migration_step_name>`

Pour définir une cible par défaut avec sqitch, ouvrir le fichier de config `sqitch.conf` et retirer les commentaires et insérer la cible sur les lignes suivantes :
```
[engine "pg"]
	target = db:pg:<targeted_database>
```

Si l'on veut passer l'étape de confirmation, on peut ajouter `-y` à la commande initiale :  
`sqitch revert -y --to <migration_phase_name>`

`sqitch revert --to HEAD^1` : permet de revert d'une étape

<br/>