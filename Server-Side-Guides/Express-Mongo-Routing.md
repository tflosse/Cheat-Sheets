# Express & Mongo Routing

This is where the Model and Controller parts of MVC come together. It is good practice to separate concerns and keep them in different files for `models` and `controllers`. In short, avoid co-locating different concerns within the design or code.

### Connect Express and Mongo

In an existing Express app, install Mongoose.

> npm i mongoose

If starting fresh, install both (refer to [Express Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Server-Side-Guides/Basics-Express.md) markdown if starting with Express)

Check that your dependencies are installed:
```js
  "dependencies": {
    "dotenv": "^8.2.0",
    "express": "^4.17.1",
    "mongoose": "^5.9.22"
  }
  ```

Following the Separation of Concerns Principle, create a separate directory for data. There, create a file where we will connect Mongoose to Mongo (our database).
> mkdir db &&
> touch db/Connection.js

**In `connection.js`**
Connect mongoose and mongo, similarly to setting up Mongoose ([Mongoose Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Server-Side-Guides/Basics-Mongoose.md))

```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/'+'mongosub-database',  
    {   useNewUrlParser: true, 
        useUnifiedTopology: true, 
        useFindAndModify: false  }, 
        () => console.log("Connection with Mongo is established.")
);

module.exports = mongoose;
```
### Models
**In `models` directory**
Create a Model (and Schema - also explained in the [Mongoose Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Server-Side-Guides/Basics-Mongoose.md) markdown.)

```js
const mongoose = require('../db/connection.js');
// Requires mongoose variable exported from the connection.js file

const entrySchema = new mongoose.Schema({
    name:  { type: String, required: true },
    color:  { type: String, required: true },
    otherProp: Boolean
    // etc.
});

const Entry = mongoose.model('Entry', entrySchema);

module.exports = Entry;
```

### Routes

An over-simplified way to look at the Express-Mongo connection is:
- Express commands determine the route
- Mongoose commands determine how the data is manipulted

**In `controllers` directory**
Create (or refactor routes previously done in [Express](https://github.com/tflosse/Cheat-Sheets/blob/master/Server-Side-Guides/Basics-Express.md))

**RESTful routes -** Here, `entries` is the controller used in the Express server.js file (or what will appear in the URL before index, show, and other routes)
> For instance, for a collection of movies:
>   ```
>   const moviesController = require('./controllers/filename');
>   app.use('/movies', moviesController);
>   ```
> The URL will be "/movies" + routes declared in controllers


| URL | HTTP Verb |  Action |
|------------|-------------|------------|
| /entries/         | GET | index |
| /entries/new      | GET | new |  
| /entries          | POST | create | 
| /entries/:id      | GET | show |      
| /entries/:id/edit | GET | edit |     
| /entries/:id      | PATCH/PUT | update |   
| /entries/:id      | DELETE | destroy |

#### Seed Route
Seeding provides "starter data" that can be worked with before added additional documents or data.
Seed data can either be added as raw data or referencing a js const or json file.
> The following assumes Seed Data lives in a seedData.js (with `module.exports = seedData`) or seedData.json file in the db directory.

***Note*** that the seed route comes before the show route, since the latter is an "/:id" route. That is to avoid reading "seed" as an id and returning an error.
```js
const seedData = require('../db/seedData');
OR
const seedData = require('../db/seedData.json');

router.get('/seed', async (req,res) => {
    await Entry.deleteMany({})
    // deletes previous existing entries to start over with Seed data 
    // that requires makign it an async function and using await:
    //"Wait for entries to be deleted, then insert seed data."
    Entry.insertMany(seedData, (err, data) => {
            if (err) console.log(err)
            else res.send(data)
        })
});
```

#### Index Route
```js
router.get('/', (req, res) => {
// Recall Express routes syntax for (request, response)
    Entry.find({}, (err, allEntries) => {
        if (err) console.log(err)
        else res.send(allEntries)
    })
    // Recall CRUD with Mongoose, here if/else statements are shortened to avoid unnecessary {}
});
```

#### Create Route
```js
router.post('/', (req, res) => {
    console.log(req.body);
    // optional: helps identify the body of the request
    // conditional for the booleanProp below is used as a precaution in case Booleans are received as Strings:
    if(req.body.booleanProp==="true") req.body.booleanProp = true;
    else req.body.booleanProp = false;
    // enter CRUD with mongoose:
    Entry.create(newData, (err, entry)=> {
        if (err) console.log(err)
        else res.send(entry)
    })
});
```

#### Show Route
```js
router.get('/:id', (req, res) => {
    // Recall Express used the following
        // res.send(entries[req.params.id]);
    // Use the same "req.params.id" with mongoose:
    Entry.findById(req.params.id, (err, entry) => {
        if (err) console.log(err)
        else res.send(entry)
    })
});
```

#### Delete Route
```js
router.delete('/:id', (req, res) => {
    Entry.findByIdAndDelete(req.params.id, (err, entry) => {
        if (err) console.log(err)
        else res.send(entry)
    })
});
```
***Note:*** Mongoose commands give multiple alternatives, consider also `findOneAndDelete()` as well as `Remove()` commands. `findById` methods return the deleted document.

#### Update Route
```js
router.put('/:id', (req, res) => {
    // conditional below serves same purpose as with Post Route:
    if(req.body.booleanProp==="true") req.body.booleanProp = true;
    else req.body.booleanProp = false;
    Entry.findByIdAndUpdate(
        req.params.id, 
        req.body, 
        {new:true},
        //new true asks to pass back the updated item
        (err, updatedEntry) => {
        if (err) console.log(err)
        else res.send(UpdatedEntry)
        })
});
```
***Note:*** Similarly to Delete commands, consider alternative commands `findOneAndUpdate()` as well as the generic `Update()` for all.

| HTTP Verb  | Mongoose Commands  |
| ------------- |:-------------:|
| POST | create() |
| GET | find(), findById(), findOne(),   |
| PUT | updateOne(), findByIdAndUpdate(), findOneAndUpdate() |
| DELETE | deleteOne(), deleteMany(), findByIdAndDelete(), findByIdAndRemove(), findOneAndDelete(), findOneAndRemove() |

`Replace` commands might also be:
- `replaceOne()`
- `findOneAndReplace()`

---
##### *Option* to test with Postman
(Postman markdown [here](https://github.com/tflosse/Cheat-Sheets/blob/master/Server-Side-Guides/Postman.md))

*Fruits Example:*
Creating:
> Switch to POST > Body
![Pre-post](https://i.imgur.com/KjnOKCK.png)
> Enter key:value pairs > SEND request
![After Sending Request](https://i.imgur.com/G63E7PJ.png)

Double-check by refreshing in browser at http://localhost:3000/fruits
```js
[
{
"_id": "5f08a017c4476551ffeff190",
"name": "orange",
"color": "orange",
"booleanProp": true,
"__v": 0
}
]
```
Deleting:
> Switch to DELETE > back to Params
![Pre-post](https://i.imgur.com/CIcewu8.png)

Updating:
> Switch to PUT > Body > Enter key:value pairs to update > SEND Request
![Pre-post](https://i.imgur.com/mo6gvWw.png)

Double-check by refreshing in browser at http://localhost:3000/fruits
```js
[
{
"_id": "5f08b790a4b9f5563ebde303",
"name": "pear",
"color": "green",
"readyToEat": false,
"__v": 0
},
{
"_id": "5f08b7a1a4b9f5563ebde304",
"name": "plum",
"color": "purple",
"readyToEat": true,
"__v": 0
}
]
```

---

### Relating Models

**In `server.js`**
Add a controller for the newModel routes:
```js
const newModelsController = require('./controllers/newModels');
app.use('/newModels', newModelsController);
```

**In `models` directory** 
Create a new file for a new model:
> touch newModels.js

```js
const mongoose = require('../db/connection');

const newModelSchema = new mongoose.Schema({
    name: { type: String, required: true },
    entries: [
        {
            ref: "Entry",
            type: mongoose.Schema.Types.ObjectId
        }
    ]
});

const NewModel = mongoose.model("NewModel", newModelSchema)
module.exports = NewModel;
```
**In `controllers` directory** 
Create a new file for a new model:
> touch newModels.js
Create controller routes for this model as follows

```js
const express = require('express');
const router = express.Router();
const NewModel = require('../models/newModels');

//index
router.get('/', (req, res) =>{
    NewModel.find({}, 
        (err, data) => {
            if (err) console.log(err)
            else res.send(data)
        })
});

// post/create
router.post('/', (req, res) =>{
    NewModel.create(
        {name: req.body.newModelName}, 
        (err, model) => {
            if (err) console.log(err)
            else res.send(model)
        })
});

module.exports = router;
```
Adding a **route that relates both models**:
This could be adding an entry to an owner, an author, or adding a tweet to an account, etc. Still in the `newModels.js` **controller** file:
```js
// require the Entry model in this newModel file
const Entry = require('../models/entries');
// put/add an Entry to the newModel
router.put('/:newModelId/addEntries/:id', (req, res) => {
    //require the Fruit route within
    Entry.findById(req.params.id, (err, entry) => {
        if (err) console.log(err)
        else {
            NewModel.findByIdAndUpdate(
                req.params.newModelId,
                {
                    $push: {
                        entries: entry.id
                    }
                },
                (err, model) => {
                    if (err) console.log(err)
                    else res.send(model)
                }
            )
        }
    })
});
```

***Fruits example cont'd:***

Testing in Postman: 
> Switch to PUT > Body > Enter follow the URL format above > SEND Request
![Pre-post](https://i.imgur.com/SBHn3qr.png)

Double-check by refreshing in browser at http://localhost:3000/owners

```js
[
{
"fruits": [
"5f08c91e3e125a5983112bc8"
],
"_id": "5f08c91b3e125a5983112bc7",
"name": "Tamara",
"__v": 0
}
]
```