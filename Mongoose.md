# Mongoose

Mongoose allows communication with the database (MongoDB) directly in a text editor (without navigating commands in a terminal). It is a Schema-based solution to model application data. Schemas create key:value pairs for the different data types used.

***How is it used?***
1. Require mongoose module
2. Set a variable reference to a mongoose Schema
3. Create the Schema
4. Create an instance of the Schema with a Model

Establish a connection with MongoDB:
1. Create a variable assigned to an instance of the database
2. Use `mongoose.connect()`
(`mongod` should be runningin the terminal for some older versions)

### Basic Set Up

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
// note Model capitalization (this Model is the instance of the Schema that will be created)
```
When working with multiple databases and models, the above might require several models and collections: 
- Mongoose module
- Models (or instances of the Schemas that are builtand used to navigate data)
- Database collections (as js file with a const holding the data(converted from JSON to be readable in JS)). *Both of the above are exported with `module.exports = varName`*

```js
//Global Config
const mongoURI = 'mongodb://localhost:27017/' + 'sub-database';
// tells Mongoose where to connect with Mongo and which sub-databases to connect to (if it doesn't exist, it will be created)
// defaults to 27017
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

### Schema Set Up

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

// this example uses a ModelName:
const ModelNameSchema = new Schema(
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
// ModelName example:
const ModelName = mongoose.model("ModelName", ModelNameSchema);
module.exports = ModelName;
```

## CRUD with Mongoose:
**In `App.js`**...

### Creating (`create` and `insert`) using the Model

##### Create (one):
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
    // close() is not necessary, but a good habit to have when testing
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

##### Insert (many):
```js
const manyDocs = [{
    prop1: "Lorem Ipsum",
    prop2: "Nunc sed porta dui. Donec porttitor rhoncus fringilla. Sed mollis semper eros et semper.",
    prop5: 212
},
{
    etc.
}];

ModelName.insertMany(manyDocs, (error, modelname) => {
    if (error) {
        console.log(error)
    } else {
        console.log(modelname)
    }
});
```


### Reading (`find`) data

Generic `find()` allows searches for entire documents, only some properties, specific properties, or specific values:
```js
ModelName.find((error, docs) => {
    console.log(docs);
});
// only the callback function
```

Find all docs and show only their `prop1` and `prop2`:
```js
ModelName.find({}, "prop1 prop2", (error, doc) => {
    console.log(doc);
});
```

```js
ModelName.find({prop1:'Lorem Ipsum'}, (error, doc) => {
    console.log(doc);
});
```

```js
ModelName.find({prop5:{$gte: 20}}, (error, docs) => {
    console.log(docs);
});
// $gte is a mongoose command that stands for "greater than or equal"
```
- `findById()` uses the ObjectID (can be used for Show Routes)
- `findOne()` stops at the first document that meets serch criteria
- `where()` is used in a query builder (second code block below) to specify nested properties:
```js
// With a JSON doc
Person.
  find({
    occupation: /host/,
    'name.last': 'Ghost',
    age: { $gt: 17, $lt: 66 },
    likes: { $in: ['vaporizing', 'talking'] }
  }).
  limit(10).
  sort({ occupation: -1 }).
  select({ name: 1, occupation: 1 }).
  exec(callback);

// Using query builder
Person.
  find({ occupation: /host/ }).
  where('name.last').equals('Ghost').
  where('age').gt(17).lt(66).
  where('likes').in(['vaporizing', 'talking']).
  limit(10).
  sort('-occupation').
  select('name occupation').
  exec(callback);
  ```

### Update (`update`) data

- `updateMany()`
- `update()`

##### Update (one):

- `findByIdAndUpdate()`
- `findOneAndUpdate()` will find the doc that meets criteria and update it (below)
- `updateOne()`

- `findOneAndReplace()`
- `replaceOne()`

```js
ModelName.findOneAndUpdate({prop1: "Lorem Ipsum"}, {prop4: false}, {new: true}, (error, doc) => {
    if (error) {
        console.log(error)
    } else {
        console.log(doc)
    }
});
```

### Deleting (`delete`) data

- `remove()` will remove **ALL INSTANCES**
- `deleteMany()`


##### Delete (one):

- `findByIdAndRemove()`
- `findOneAndRemove()`
or
- `deleteOne()`
- `findByIdAndDelete()`
- `findOneAndDelete()`

```js
ModelName.deleteOne({prop1:"Lorem Ipsum"}, (error, doc) => {
    if (error) {
        console.log(error)
    } else {
        console.log(doc)
    }
});
```
> Console reads:
> { n: 1, ok: 1, deletedCount: 1 }


#### Mongoose Docs:

Mongoose index for `Getting Started` is [here](https://mongoosejs.com/docs/index.html).
The short list of CRUD operations for Mongoose is available at: [Mongoose.js](https://mongoosejs.com/docs/models.html)


