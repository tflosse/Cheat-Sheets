# Deploying using Heroku

### Installing

##### Heroku & Heroku CLI

##### MongoDB Atlas


##### Deploying

(...)

**In the `connection.js` file in `db` server-side directory**
Code for the environmental variables:
```js
// Previously:
// let MONGODB_URI = 'mongodb://127.0.0.1:27017/itemsDatabase'
// Replaced by below block for deployment:
let MONGODB_URI = '';
if(process.env.NODE_ENV === 'production') {
    // DB_URL will be used by Heroku to connect to Mongo Atlas DB
    MONGO_URI = process.env.DB_URL
} else {
    MONGODB_URI = 'mongodb://127.0.0.1:27017/itemsDatabase'
}
```
**In terminal:**
> `heroku config:set DB_URL` + previously provided link
```
heroku config:set DB_URL="mongodb+srv://dbadmin:dbisthepassword@cluster0.pik2n.mongodb.net/<dbname>?retryWrites=true&w=majority"
Setting DB_URL and restarting ⬢ vibrant-madame-80674... done, v4
DB_URL: mongodb+srv://dbadmin:dbisthepassword@cluster0.pik2n.mongodb.net/<dbname>?retryWrites=true&w=majority
```
Check for Config vars in the heroku App `Settings`:
![Config Vars](https://i.imgur.com/Qq2ZrJz.png)

> `git push heroku master`

Terminal response below for reference:
```
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 403 bytes | 201.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Compressing source files... done.
remote: Building source:
remote: 
remote: -----> Node.js app detected
remote:        
remote: -----> Creating runtime environment
remote:        
remote:        NPM_CONFIG_LOGLEVEL=error
remote:        NODE_ENV=production
remote:        NODE_MODULES_CACHE=true
remote:        NODE_VERBOSE=false
remote:        
remote: -----> Installing binaries
remote:        engines.node (package.json):  unspecified
remote:        engines.npm (package.json):   unspecified (use default)
remote:        
remote:        Resolving node version 12.x...
remote:        Downloading and installing node 12.18.2...
remote:        Using default npm version: 6.14.5
remote:        
remote: -----> Restoring cache
remote:        - node_modules
remote:        
remote: -----> Installing dependencies
remote:        Installing node modules (package.json + package-lock)
remote:        audited 83 packages in 0.956s
remote:        
remote:        1 package is looking for funding
remote:          run `npm fund` for details
remote:        
remote:        found 0 vulnerabilities
remote:        
remote:        
remote: -----> Build
remote:        
remote: -----> Caching build
remote:        - node_modules
remote:        
remote: -----> Pruning devDependencies
remote:        audited 83 packages in 0.907s
remote:        
remote:        1 package is looking for funding
remote:          run `npm fund` for details
remote:        
remote:        found 0 vulnerabilities
remote:        
remote:        
remote: -----> Build succeeded!
remote: -----> Discovering process types
remote:        Procfile declares types     -> (none)
remote:        Default types for buildpack -> web
remote: 
remote: -----> Compressing...
remote:        Done: 24.2M
remote: -----> Launching...
remote:        Released v6
remote:        https://vibrant-madame-80674.herokuapp.com/ deployed to Heroku
remote: 
remote: Verifying deploy... done.
To https://git.heroku.com/vibrant-madame-80674.git
   6075d09..e134605  master -> master
```

> `heroku logs --tail` 

Renders the whole log.

> `heroku run node db/seedItems.js`

```js
Running node db/seedItems.js on ⬢ vibrant-madame-80674... up, run.5942 (Free)
Successfully connected to MongoDB.
Created some items!
```

### Deploying React

#### `apiConfig`

**In console:**
There is a js variable that knows the current location:
> window.location.hostname

**In `src` folder, create `apiConfig.js`:**
```js
let apiUrl = '';

const apiUrls = {
  production: 'https://sei-items-api.herokuapp.com/api',
  // above should be the herokuapp URL
  development: 'http://localhost:4000/api'
}

if (window.location.hostname === 'localhost') {
  apiUrl = apiUrls.development
} else {
  apiUrl = apiUrls.production
}

export default apiUrl;
```

Then, **`import apiUrl from '../../apiConfig'`** where needed.
AND replace all instances of http://localhost:4000/api with the URL (localhost) with `${apiURL}`:

For instance:

```js
import React, { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import axios from 'axios';
import apiUrl from '../../apiConfig';

const Items = (props) => {
  const [items, setItems] = useState([])
  useEffect(() => {
    const makeAPICall = async () => {
      try {
          // Replaced on line below
          // Previously: `http://localhost:4000/api/items/`
        const response = await axios(`${apiUrl}/items`)
        setItems(response.data.items)
      } catch (err) {
        console.error(err)
      }
    }
    makeAPICall()
  }, [])
  ```

**Install Surge**
> npm i --global surge

>cd client && 
npm run build
in the Client folder where React lives

In `package.json` scripts:
```js
"build": "react-scripts build",
```
It will initiate a `Build` Folder with:
- index.html
- css
- js

If React app is using React Router then change into the build directory and rename the `index.html => 200.html`.

> cd build && 
`mv index.html 200.html`
index.html file name needs to be changed to `200.html` if React-Router is used

Run the surge command and follow the prompts.

> surge

Follow prompts.
Terminal will show:
![Surge terminal](https://i.imgur.com/kCmB9jg.png)


