# ESLint

## Installation & paramétrages

`npm init @eslint/config`

```
- To check syntax, find problems, and enforce code style
- JavaScript modules (import/export) ESM (ECMAScript Modules)
- None of these
- TypeScript : No
- Node only (appuyer sur "espace" pour décocher "browser" et sélectionner "node" avec la même touche)
- Use a popular style guide : Airbnb
- Format : JSON
- Install dependencies : Yes
- Package Manager : npm
```

Quand on utilise les JavaScript modules, il va falloir préciser à notre application qu'on les utilise. Il faut ajouter `"type": "module"` dans le package.json. Il permet en plus de faire des import/export de faire des await à la racine des scripts (tous les fichiers JS). C'est à dire que le contexte global est déjà asynchrone et donc de pouvoir utiliser les `await` directement (sans devoir faire de fonction asynchrone).

<br/>

Extension `ESLint` qui formate en fonction des règles ESLint + paramétrage de celle-ci :
1. CTRL+SHIFT+P
2. "user settings" (paramètres utilisateur)
3. Ajouter :
```json
"[javascript]" : {
    "editor.defaultFormatter" : "dbaeumer.vscode-eslint"
}
```
4. Formattage automatique au moment de la sauvegarde en ajoutant :
```json
"editor.codeActionsOnSave" : {
  "source.fixAll.eslint": "explicit"
}
```
5. Pour régler le problème d'erreur au niveau des extensions, il faut ajouter dans les `"rules"` du fichier `.eslintrc.json` : 
```json
"import/extensions": ["error", "always"]
```
NB : pas compatible avec la sauvegarde auto (il faut faire une sauvegarde manuelle pour formatter).  

6. Pour la team double quotes, ajouter au même endroit :
```json
"quotes": ["error", "double"]
```
<br/>  

- Si jamais les import sont surlignés avec une erreur `'XXXX' should be listed in the project's dependencies. Run 'npm i -S XXXX' to add it`, il s'agit d'un bug de VSC et la seule solution est de relancer l'éditeur.

- On créer un module pour les variables d'environnement et on l'import pour éviter le formatage auto d'ESLint : celui-ci essaye de regrouper les imports mais de ce fait les variables ne sont pas accessibles au router.