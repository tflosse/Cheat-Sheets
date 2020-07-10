#Express

###Basic Set Up

Install basics can also be found here [here](https://expressjs.com/en/starter/installing.html).

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

**Dotenv** is a zero-dependency module that loads environment variables from a `.env` file into `process.env`. it stores configuration in the environment separate from code.

```js
// in terminal
npm i dotenv
touch .env
```
```js
// in the .env file
PORT = 3000
// or port corresponding to the one used above.
```

Getting started with backend and server-side, there might be a need to install `nodemon` - `nodemon` is a tool that helps develop node. js based applications by automatically restarting the node application when file changes in the directory are detected.
```js
// this instance:
npm i nodemon
// globally:
npm i nodemon -g
OR sudo npm i nodemon -g
// "sudo" overrides permissions - use it sparingly
```

Create a `.gitignore` file, this prevents files listed within from being pushed to git
```js
touch .gitignore
// in .gitignore file add
node_modules
.env
```

