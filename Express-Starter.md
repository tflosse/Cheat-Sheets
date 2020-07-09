#Express

###Basic Set Up

**Terminal commands**
```js
mkdir or git clone <your repo url>
touch server.js
// creates a new js file
npm init
// initializes node
// adds package.json where
    scripts: "main":"server.js"
    // if server.js is not created prior, 
    // main will be "index.js" by default
```
Alternatively `npm init -y` will init with default settings
```js
npm i express
// installs express, adds dependencies
// + installs additional modules and adds package-lock.json
```

**In `server.js`**
Express uses `require` rather than `import` as seen in React and some other frameworks.
```js
const express = require('express');
const app = express();
// creates an instance of Express
const port = 3000;
// here use the port used with localhost
OR
const port = process.env.PORT || 3000
// above will be used with dotenv 
// tells what port we are going to  be using
```

