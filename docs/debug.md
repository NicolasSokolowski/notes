# Debug

## Installation

`npm i -D debug`

Prefixer le script "dev" comme tel :

```json
"dev": "DEBUG=app:* npx nodemon"
```
Dans le fichier de connexion au client (ex: `pg.client.js`) :

```js
import pg from "pg";

import debug from "debug";

const debugSQL =debug("app:SQL");
```
La connexion au client :
```js
const client = new pg.Pool({
  host: process.env.PGHOST,
  user: process.env.PGUSER,
  database: process.env.PGDATABASE,
})
```
Et enfin la mise en place de debug :

```js
const debugClient = {
  originalClient: client,
  query(...args) {
    debugSQL(...args);
    return client.query(...args)
  }
}

export default debugClient
```



