#Mongoose

Mongoose allows communication with the database directly in a text editor (without navigating commands in a terminal).

###Basic Set Up

**Terminal commands**
```js
mkdir or git clone <your repo url>
touch app.js
npm init -y
// Similarly to Express Set UP:
// initializes node, adds package.json where
    scripts: "main":"server.js"
    // if server.js is not created prior, 
    // main will be "index.js" by default
```

```js
npm i mongoose
// installs mangoose, adds dependencies
// + installs additional modules and adds package-lock.json
  "dependencies": {
    "mongoose": "^5.9.22"
  }
touch modelname.js
// This file will hold the SCHEMA
```

**In `App.js`**
Mongoose also uses `require`
```js 
const mongoose = require('mongoose');
const Model = require('./modelname');
// note Model capitalization
```

```js
//Global Config
const mongoURI = 'mongodb://localhost:27017/' + 'sub-database';
// tells Mongoose where to connect with Mongo and which sub-databases to connect to (if it doesn't exist, it will be created)
const db = mongoose.connection;
// sets shorthand for mongoose.connection

// Connect to Mongo
mongoose.connect(mongoURI)
```

Test running the App with **terminal command** `nodemon`
(nodemon might have to be installed if not installed globally)
> might get deprecation warnings
Add a callback function in `mongoose.connect()`
```js
mongoose.connect(mongoURI,  
    { useNewUrlParser: true, useUnifiedTopology: true, useFindAndModify: false },
    () => {
      console.log('Connection is established.')
    });
```

**Option** to create callback function for events signaling connection success or error:
```js
db.on('error', err => console.log(err.message + ' is mongod not running?'))
db.on('connected', () => console.log('mongo connected: ', mongoURI))
db.on('disconnected', () => console.log('mongo disconnected'))
```
**Option** to add a time out to close the database connection:
```js
setTimeout(() => {
    db.close();
}, 5000)
```
*Terminal after saving:*
> mongo disconnected
> [nodemon] clean exit - waiting for changes before restart

###Schema Set Up

**In `modelname.js`**
Schema is the "shape" of our document.
```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
// sets shorthand for mongoose.schema
```

Create a new Schema that will define what the document format will be - it defines object properties and data types.

```js
const mySchema = new Schema(
    {
        prop1: String,
        prop2: String,
        prop3: {type: Number, default: 0},
        prop4: {type: Boolean, default: true},
        prop5: {type: Number, require: true}
    },
    {timestamps: true}
)

// this example uses a tweet:
const tweetSchema = new Schema(
    {
        title: String,
        body: String,
        author: String,
        likes: {type: Number, default: 0},
        sponsored: {type: Boolean, default: false}
    },
    {timestamps: true}
);
```

Create a model (responsible for creating documents) - takes in the schema, but the model itself is what interacts with the database.

```js
const ModelName = mongoose.model("ModelName", mySchema)
//invokes model, names it, and tells it what Schema to follow
module.exports = ModelName;
// exports this model
```
```js
// tweet example:
const Tweet = mongoose.model("Tweet", tweetSchema);
module.exports = Tweet;
```

###Using the Model

**In `App.js`**

#####Hard-coded here:
```js
const myDoc = {
    prop1: "Lorem Ipsum",
    prop2: "Nunc sed porta dui. Donec porttitor rhoncus fringilla. Sed mollis semper eros et semper.",
    prop5: 212
}

ModelName.create(myDoc, (error, modelname) => {
    if (error) {
        console.log(error)
    } else {
        console.log(modelname)
    }
    db.close();
});
```
*Running in terminal with show:*
```js
mongo connected:  mongodb://localhost:27017/sub-database
Connection is established.
{
  prop3: 0,
  prop4: true, 
  _id: 5f0754a59afda2347bac280b,
  prop1: 'Lorem Ipsum',
  prop2: 'Nunc sed porta dui. Donec porttitor rhoncus fringilla. Sed mollis semper eros et semper.',
  prop3: 212,
  createdAt: 2020-07-09T17:32:21.854Z,
  updatedAt: 2020-07-09T17:32:21.854Z,
  __v: 0
}
mongo disconnected
[nodemon] clean exit - waiting for changes before restart
```

