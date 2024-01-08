# JEST

Il s'agit d'un module de test unitaire

## <ins>Installation</ins>

`npm i -D jest`

Installation en environnement de dev car il n'y a pas d'intérêt pour la prod !

<ins>Initialisation</ins>

`npm init jest@latest`

```
✔ Would you like to use Jest when running "test" script in "package.json"? … yes
✔ Would you like to use Typescript for the configuration file? … no
✔ Choose the test environment that will be used for testing › node
✔ Do you want Jest to add coverage reports? … yes
✔ Which provider should be used to instrument code for coverage? › v8
✔ Automatically clear mock calls, instances, contexts and results before every test? … no
```

Les tests doivent être créés dans un dossier `__tests__`. Les fichiers de test quant à eux doivent être se terminés par `spec.js` ou `test.js`.

Jest ne peut pas utiliser les modules, il faut modifier le script `test` dans le package.json :

`"NODE_OPTIONS=-experimental-vm-modules npx jest"`

### Plugin ESLint Jest

`npm i -D eslint-plugin-jest`

Ajouter dans les config ESLint :

```json
"plugins": ["jest"]
```

Dans les `"rules"` :
```json
"jest/no-disabled-tests": "warn",
"jest/no-focused-tests": "error",
"jest/no-identical-title": "error",
"jest/prefer-to-have-length": "warn",
"jest/valid-expect": "error"
```

Et dans le `"env"` :
```json
"jest/globals": true
```

---

### Les méthodes Jest

- `test()` permet de déclencher un test. Un descriptif de ce qui est testé doit être inséré entre guillements dans les paranthèses.
`test("cadexService is an object", () => {});`
- `expect()` indique qu'un résultat spécifique est attendu. Il est couplé à un 2ème élement, comme par exemple :
1. `toBe()` : L'élément testé doit être de la nature attendue. *exemple* : 
```js
test("cadexService is an object", () => {
  expect(typeof cadexService).toBe("object");
});
```
2. `toHaveProperty()` : L'élément testé doit avoir une propriété désignée
*exemple* :
```js
test("must return an object with a name", () => {
  expect(cadex).toHaveProperty("name");
});
```
3. `ToBeGreaterThan()` : L'élément testé doit être plus grand que. *exemple* :
```js
test("must return an object with a string name property", () => {
  expect(cadex.name.length).toBeGreaterThan(1);
});
```
4. `toBeGreaterThanOrEqual()` : L'élément doit être égal ou plus grand que. *exemple* :
```js
test("the string must have at least 4 words", () => {
  const cadex = cadexService.generate();
  const stringArray = cadex.toString().split(" ");
  expect(stringArray.length).toBeGreaterThanOrEqual(4);
});
```
5. `toBeTruthy()` : permet de vérifier si l'élément testé est vrai. 
6. `toMatch()` : l'élément testé doit être inclus dans. 
- `describe()` permet de regrouper plusieurs tests, d'organiser de manière plus claire le fichier et de compartimenter les résultats. Cette méthode n'a aucun effet sur les tests.

---

### Notes

Ne jamais basé les tests sur des données externes !  
Les données doivent être créées de toutes pièces pour être fournies aux tests.