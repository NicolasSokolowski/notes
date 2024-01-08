# Supertest

Supertest est un module qui créer un environnement de test qui permet de simuler un appel http sans vraiment le faire : il n'est pas nécessaire de lui fournir un nom de domaine/ip et/ou le port.

## <ins>Installation</ins>

`npm i -D supertest`

---

```js
import request from "supertest";

import app from "../app/index.app.js";

test("route GET /cadex", async () => {
  const response = await request(app)
    .get("/cadex")
    .set("Accept", "application/json")
    .expect(200)
    .expect("Content-Type", /json/);

  const responseContent = response.body;
  expect(responseContent).toHaveProperty("cadex");
  expect(typeof responseContent.cadex).toBe("string");
});
```

Ici, on teste notre app avec `supertest` dans un contexte asynchrone :

`const response = await request(app)` : génère l'environnement de test de l'app  
`.get("/cadex")` : la route qui doit être testée  
`.set("Accept", "application/json")` : le format de données accepté  
`.expect(200)` : le status de la réponse attendu  
`.expect("Content-Type", /json/)` : le format de données attendu

On stocke le body de `response` dans une variable et on peut réaliser nos vérifications.