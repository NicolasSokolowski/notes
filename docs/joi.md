## Joi

Joi permet de créer un schéma de validation qui va définir le format que doit prendre l'object reçu, qu'il vienne d'une route `post` ou d'une autre source.  

[Doc de Joi](https://joi.dev/api/?v=17.9.1)

<ins>Installation</ins>

`npm i joi`

```js
import joi from "joi";
```

Définition d'un schéma :
```js
export default Joi.object({
  name: Joi.string()
    .alphanum()
    .min(2),
  adjective: Joi.string()
    .alphanum()
    .min(2),
  verb: Joi.string()
    .alphanum()
    .min(1),
  complement: Joi.string()
    .alphanum()
    .min(2),
}).required().min(1);
```

Il est possible d'utiliser un patter avec `.pattern()` auquel on peut associe un RegEx précédemment défini dans une variable.