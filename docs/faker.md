# Faker

Faker permet la génération de fausses données mais en restant cohérent.

## Installation

```
npm i --save-dev @faker-js/faker
```

---

```js
import { faker } from "@faker-js/faker";
```
Et on déclare une fonction pour générer ses données suivant certains critères :
```js
export function createRandomUser() {
  return {
    userId: faker.string.uuid(),
    username: faker.internet.userName(),
    email: faker.internet.email(),
    avatar: faker.image.avatar(),
    password: faker.internet.password(),
    age: faker.number.int({min: 18, max: 90}),
    registeredAt: faker.date.past(),
  };
}
```

On créer une fonction qui va générer X utilisateurs :
```js
function batchCreateUser(count = 100) {
  const emptyArray = [...new Array(count)]
  return emptyArray.map(() => createRandomUser());
}
```

Pour avoir une idée de la performance de la création des données, on utilise des commandes console qui vont être placées avant et après la fonction qui doit être analysée :

```js
console.time(`Création de la collection d'utilisateurs`);
console.log(batchCreateUser(10000));
console.timeEnd(`Création de la collection d'utilisateurs`);
```

On aura un ici le temps qu'il aura fallu pour générer 10000 utilisateurs.

