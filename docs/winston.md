# Notes

## Winston

### <ins>Installation</ins>

Winston est une librairie qui permet de journaliser les informations via un canal de transport sur différents points de sortie : le terminal, un fichier etc. Il est possible de définir plusieurs points de sortie pour la même information ! Il faudra dans ce cas spécifier plusieurs canaux de transports pour cette même information.

```
npm i winston
```

---

Ne pas oublier d'importer Winston :
```js
import winston from "winston";
```

Il faut déclarer les méthodes pour pouvoir les utiliser :
```js
const {
  combine, timestamp: now, label: category, printf, json, colorize,
} = winston.format;
```

Et définir un format :
```js
const consoleFormat = printf(({
  level, message, label, timestamp,
}) => `${timestamp} [${label}] ${level}: ${message}`);
```

Définir un nouveau logger :
```js
const logger = winston.createLogger();
```

Et lui attribuer un ou plusieurs transports :
```js
const logger = winston.createLogger({
  transports: [
    new winston.transports.Console({
      level: "http",
      format: combine(
        category({ label: "all" }),
        now(),
        consoleFormat,
      ),
    }),
    new winston.transports.File({
      level: "http",
      filename: "./logs/combined.log",
      format: combine(
        now(),
        json(),
      ),
    }),
  ],
});
```

Pour créer un logger vers la console :
```js
new winston.transports.Console();
```

Pour créer un logger vers un fichier :
```js
new winston.transports.File();
```

Le `level` détermine si l'information est envoyée. S'il est égal ou inférieur au niveau, l'information est envoyée.
```js
level: "http",
```

Pour définir le format :
```js
format: combine(
  category({ label: "all" }),
  now(),
  colorize() // permet la mise en couleur
  consoleFormat,
),
```

Dans le cas de la journalisation vers un fichier, il faut déterminer le fichier vers lequel vont être envoyés les informations :
```js
filename: "./logs/combined.log",
```

## Extension : Winston Daily Rotate File

### <ins>Installation</ins>


```
npm i winston-daily-rotate-file
```

Winston Daily Rotate File permet la rotation de fichier (archivage) et gère la suppression de ces fichiers.

```js
import DailyRotateFile from "winston-daily-rotate-file";
```

On peut ensuite ajouter davantage d'options :
```js
datePattern: "YYYY-MM-DD-HH-mm",
zippedArchive: true,
frequency: "1m",
maxFiles: "2m",
```

`datePattern: "YYYY-MM-DD-HH-mm"` : format de la date  
`zippedArchive: true` : définit si le fichier doit être compressé ou non  
`frequency: "1m"` : fréquence de création de la rotation  
`maxFiles: "5d"` : délai avant la suppression du fichier