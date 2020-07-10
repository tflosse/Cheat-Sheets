# Express & Mongo Routing

This is where the Model and Controller parts of MVC come together. It is good practice to practice separation of concerns and keep them in separate files for `models` and `controllers`. In short, avoid co-locating different concerns within the design or code.

### Connect Express and Mongo

In an existing Express app, install Mongoose.

> npm i mongoose

If starting fresh, install both (refer to [Express Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Basics-Express.md) markdown as a good place to start)

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
Connect to mongoose and mongo, similarly to setting up Mongoose ([Mongoose Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Basics-Mongoose.md))

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
#### Models
**In `models` directory**
Create a Model (and Schema - also explained in the [Mongoose Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Basics-Mongoose.md) markdown.)

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

#### Routes
**In `controllers` directory**
Create (or refactor routes), previous done in Express ([Express Basics](https://github.com/tflosse/Cheat-Sheets/blob/master/Basics-Express.md))

###### Index
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

##### Create
```js
router.post('/', (req, res) => {
    console.log(req.body);
    // optional: helps identify the body of the request
    if(req.body.readyToEat==="true") req.body.readyToEat = true;
    else req.body.readyToEat = false;
    const newData = req.body;
    // enter CRUD with mongoose:
    Entry.create(newData, (err, entry)=> {
        if (err) console.log(err)
        else res.send(entry)
    })
});
```

##### Show Route
```js
router.get('/:id', (req, res) => {
    // Recall Express used the following
    // res.send(fruits[req.params.id]);
    // Use the same "req.params.id" with mongoose:
    Entry.findById(req.params.id, (err, entry) => {
        if (err) console.log(err)
        else res.send(entry)
    })
});
```

##### *Option* to test with Postman
(Postman markdown [here](https://github.com/tflosse/Cheat-Sheets/blob/master/Postman.md))

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
