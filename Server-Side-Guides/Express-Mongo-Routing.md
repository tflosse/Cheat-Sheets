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
> mkdir db
> touch Connection.js

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
Create (or refactor routes), previous done in Express ([Express Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Server-Side-Guides/Basics-Express.md), brief recall of RESTful routes also available there).

#### Index Route
```js
router.get('/', (req, res) => {
// Recall Express routes syntax for (request, response)
    Fruit.find({}, (err, allEntries) => {
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
    if(req.body.otherProp==="true") req.body.otherProp = true;
    else req.body.otherProp = false;
    const newData = req.body;
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
    // With hard-coded array in Express, the code for the route was:
        // const deletedEntry = entries.splice(req.params.id, 1);
        // res.send(deletedEntry);
    // Mongoose commands give multiple alternatives, consider also "findByIdAndDelete" as well as "Remove" commends
    Entry.findOneAndDelete(req.params.id, (err, entry) => {
        if (err) console.log(err)
        else res.send(entry)
    })
});
```


##### *Option* to test with Postman
(Postman markdown [here](https://github.com/tflosse/Cheat-Sheets/blob/master/Server-Side-Guides/Postman.md))

*Fruit Example*
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
"readyToEat": true,
"__v": 0
}
]
```
