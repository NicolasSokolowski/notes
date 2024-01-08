## Commentaires JSDoc

Les commentaires JSDoc ont pour but de donner du détail au code en documentant les fonctions, les classes, les modules etc.  
Ils sont généralement placés avant le code commenté. Ils commencent par `/**` et finissent par `*/`.  
Les commentaires JSDoc n'ont aucun effet sur le code.

[Doc JSDoc](https://jsdoc.app/)

```js
  /**
   * Method which permit to inject data in the service
   * @param { PartsCollection } data object that contains differents collections of sentence parts
   */
  setData(data) {
    this.data = data;
  }
```

`@param` permet de documenter un paramètre attendu.

Les custom types peuvent également être documentés avec `@typedef` :  
```js
/**
 * @typedef { Object } PartsCollection
 * @property { string[] } names Nouns collection
 * @property { string[] } adjectives Adjectives collection
 * @property { string[] } verbs Verbs collection
 * @property { string[] } complements Complements collection
 */
```

`@property` documente une propriété.  
`@returns` documente le return.  
`@method` documente une méthode.
```js
/**
 * @typedef CadexObject
 * @type { Object }
 * @property { string } name Cadex random name
 * @property { string } adjective Cadex random adjective
 * @property { string } verb Cadex random verb
 * @property { string } complement Cadex random complement
 * @method toString Return a complete literal cadex
 */
```