## Snippets

[Générateur de snippets](https://snippet-generator.app/)

Pour ajouter un snippet, il faut aller dans les paramètres de VSC > Extraits de code de l'utilisateur (user snippets) > Choisir le langage et y insérer le snippet.  *exemple* :  
```json
"create table with date": {
"prefix": "create",
"body": [
  "CREATE TABLE \"${1:tablename}\" (",
  "  \"id\" INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,",
  "  \"created_at\" TIMESTAMPTZ NOT NULL DEFAULT NOW(),",
  "  \"updated_at\" TIMESTAMPTZ",
  ")"
],
"description": "create table with id, created_at, updated_at"
}
```