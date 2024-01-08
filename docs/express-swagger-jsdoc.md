# Express-Swagger-JSDoc

## <ins>Installation</ins>

```
npm i express-jsdoc-swagger
```

```js
import expressJSDocSwagger from "express-jsdoc-swagger";
```
<details>
<summary>Options</summary>

```js
const options = {
  info: {
    version: "1.0.0",
    title: "Cadavre exquis alias Cadex",
    license: {
      name: "MIT",
    },
  },
  security: {
    BasicAuth: {
      type: "http",
      scheme: "basic",
    },
  },
  // Base directory which we use to locate your JSDOC files
  baseDir: __dirname,
  // Glob pattern to find your jsdoc files (multiple patterns can be added in an array)
  filesPattern: "./**/*.js",
  // URL where SwaggerUI will be rendered
  swaggerUIPath: "/api-docs",
  // Expose OpenAPI UI
  exposeSwaggerUI: true,
  // Expose Open API JSON Docs documentation in `apiDocsPath` path.
  exposeApiDocs: false,
  // Open API JSON Docs endpoint.
  apiDocsPath: "/",
  // Set non-required fields as nullable by default
  notRequiredAsNullable: false,
  // You can customize your UI options.
  // you can extend swagger-ui-express config. You can checkout an example of this
  // in the `example/configuration/swaggerOptions.js`
  swaggerUiOptions: {},
};
```
</details>

<br/>

--- 
On créer une factory et on lui donne accès à l'application :
```js
export default (app) => expressJSDocSwagger(app)(options);
```

Pour que la factory ait accès à l'app (dans l'`index.js`):  
```js
import createDoc from "./app/helpers/swagger.doc.js";

createDoc(expressApp);
```

Il faut dans le module créé modifier la variable `__dirname` :  
```js
import * as url from "node:url";

// La variable __dirname (répertoire courant du script courant) n'est pas
// accessible en ESM, on doit donc la générer nous-même.
// On fait en sorte que ce soit le répertoire racine de notre application "app"
const dirname = url.fileURLToPath(new URL("..", import.meta.url));
```

La documentation est accessible sur la route suivante :
```js
swaggerUIPath: "/api-docs",
```

On peut activer la documentation de l'API :
```js
exposeApiDocs: true,
```
et définir sa route :
```js
 apiDocsPath: "docs",
```

---

Pour documenter une route :
```js
router.route("/cadex")
  /**
   * GET /cadex
   * @summary Get customized cadex or not
   * @param {string} name.query - customized name for the generated cadex
   * @param {string} adjective.query - customized adjective for the generated cadex
   * @param {string} verb.query - customized verb for the generated cadex
   * @param {string} complement.query - customized complement for the generated cadex
   * @return {LiteralCadexObject} 200 - success response - application/json
   * @return {Error} 400 - Bad request response
   * @return {Error} 500 - Internal Server Error response
   */
  .get(
    validateMiddlewareFactory("query", getSchema),
    controllerWrapper(controller.getCadex),
  )
  /**
   * POST /cadex
   * @summary Get customized cadex or not
   * @param {InputCadexPartsObject} request.body.required - customized parts for the generated cadex
   * @return {LiteralCadexObject} 200 - success response - application/json
   * @return {Error} 400 - Bad request response
   * @return {Error} 500 - Internal Server Error response
   */
  .post(
    validateMiddlewareFactory("body", postSchema),
    controllerWrapper(controller.postCadex),
  );
```