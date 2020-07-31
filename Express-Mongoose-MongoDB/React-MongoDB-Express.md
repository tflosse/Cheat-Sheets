# MERN

#### MongoDB, Express, React, Node
![MERN simplified](https://i.imgur.com/G8gIzCw.png)

### Getting started
Ensure modules and dependencies are installed.
```
npm install
```
##### MongoDB Compass Tool
After initializing, Compass will default to local mongo dbs when clicking "Connect" under `New Connection` - the tool makes it easier to navigate databases.
![MongoDB Compass](https://i.imgur.com/cGPFsKe.png)


### Setting up React 

Install react, Router, and Router-DOM as a `Client` directory in the App (create it if necessary (`mkdir`))
```
npx create-react-app app-name
npm i react-router
npm i react-router-dom
```
> Ports might need to be changed depending on where Express and React are running.

```
cd Client (or the React App)
npm start
//"npm start" should be sripted (in pacjage.json) as "node server.js"
```

Additionally, it is good practice to create a `Components` directory and have React Components in `Routes` and `Shared` directories where applicable.
ej. Routes:
- Home.jsx
- Items.jsx
- Item.jsx
- ItemCreate.jsx
- ItemEdit.jsx

Shared:
- Nav.jsx
- Layout.jsx
- Footer.jsx

#### Setting up React Components

**`Layout.jsx`**
```js
import React from 'react';
import Nav from './Nav';
// from Nav component (code below)
import Footer from './Footer';
// If a footer component is added

const Layout = props => {
    <div>
        <h1>Items App</h1>
        <Nav />
        {props.children}
        <Footer />
        // If a footer component is added
    </div>
};

export default Layout;
```
Details about the use of [props.children](https://reactjs.org/docs/react-api.html#reactchildren)

**`Nav.jsx`**
[NavLinks](https://reactrouter.com/web/api/NavLink) is a good resource for Nav Rect Components
```js
import React from 'react';
import { NavLink } from 'react-router-dom';

const Nav = () => {
    return (
  <nav>
    <NavLink to='/'>Home</NavLink>
    <NavLink to='/items'>Items</NavLink>
    <NavLink to='/create-item'>Create Item</NavLink>
  </nav>
)};

export default Nav;
```

**`Home.jsx`**
```js
import React from 'react';
import Layout from '../shared/Layout';

const Home = () => {
    return (
  <Layout>
    <h4>Welcome to the items app!</h4>
  </Layout>
)};

export default Home;
```

#### Creating a Route in React

**In `App.js`**
Import `withRouter` - gives access to the history object’s properties and the closest `Route`'s match via the withRouter higher-order component. It passes updated `match`, `location`, and `history` props to the wrapped component whenever it renders. ([withRouter `Docs`](https://reactrouter.com/core/api/withRouter))
 > It does not subscribe to location changes like React Redux’s connect does for state changes. Instead, re-renders after location changes propagate out from the Router component. This means that withRouter does not re-render on route transitions unless its parent component re-renders.

```js
import React from 'react';
import {Route, Switch, withRouter} from 'react-router-dom';
import Home from './Components/routes/Home';
import './App.css';

function App() {
  return (
    <>
      <Switch>
        <Route exact path='/' component={Home} />
      </Switch>
    </>
  )
};

export default withRouter(App);
```

#### Axios
In the `React App` directory (named `Client` here), install Axios:
> npm i axios

**In the `Items.jsx` component:**

Break down in comments:
```js
import React, { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import axios from 'axios';

const Items = (props) => {
  const [items, setItems] = useState([])
  useEffect(() => {
    const makeAPICall = async () => {
      try {
        const response = await axios(`http://localhost:3001/api/items`)
        // localhost:3000 above would be the same as the port in the server.js file
        // axios is used to make the call
        setItems(response.data.items)
      } catch (err) {
        console.error(err)
      }
      // Catches error and "logs" it with conosole.error
    }
    makeAPICall()
  }, [])
 const itemsArr = items.map( item => ( 
        <li key={item._id}>
            <Link to={`/items/${item._id}`}>{item.title}</Link>
        </li>
    )
  )
    return (
      <>
        <h4>Items</h4>
        <ul>
          {itemsArr}
        </ul>
      </>
    )
}
export default Items;
```

**Add Items as a Route in `App.js`:**
```js
import Home from './Components/routes/Home';
import Items from './Components/routes/Items';
// (...)
// In the App function, now return:
    <Switch>
        <Route exact path='/' component={Home} />
        <Route path='/items' component={Items} />
    </Switch>
```
Ensure the server side is running (in this example `npm run start`) on one port, and react on another. When both are running, clicking on Items from the Nav links, should now render the Items' page (including the items that have been read from the server side).

![example](https://i.imgur.com/s4AI4xx.png)

**In the `Item.jsx` component:**

```js
import React, { useState, useEffect } from 'react'
import { Link, Redirect } from 'react-router-dom'
import axios from 'axios'

import Layout from '../shared/Layout'

const Item = (props) =>  {
  const [item,setItem] = useState(null)
  const [isDeleted,setIsDeleted] = useState(false)

  useEffect(() => {
    const makeAPICall = async () => {
      try {
          const response = await axios(`http://localhost:3001/api/items/${props.match.params.id}`)
          console.log('Item - response', response)
          setItem(response.data.item)
        } catch (err) {
          console.error(err)
        }
  
   }
  makeAPICall()
  }, [])

  const destroy = async () => {
    const response = await axios({
      url: `http://localhost:3001/api/items/${props.match.params.id}`,
      method: 'DELETE'
    })
    setIsDeleted(true)
  }

    if (!item) {
      return <p>Loading...</p>
    }

    if (isDeleted) {
      return <Redirect to={
        { pathname: '/', state: { msg: 'Item succesfully deleted!' } }
      } />
      // Redirect is used to go to the new URL
    }

    return (
      <Layout>
        <h4>{item.title}</h4>
        <p>Link: {item.link}</p>
        <button onClick={destroy}>Delete Item</button>
        <Link to={`/items/${props.match.params.id}/edit`}>
          <button>Edit</button>
        </Link>
        <Link to="/items">Back to all items</Link>
      </Layout>
    )
}

export default Item;
```
 **Update `App,js` to include this `Item route`:**

 ```js
 import Home from '../components/routes/Home'
import Items from './components/routes/Items'
import Items from './components/routes/Item'

const App = props => (
  <>
    <Switch>
      <Route exact path='/' component={Home} />
      <Route path="/items/:id" component={Item} />
      // Item route should be above the items route because of Switch, 
      // alternatively, use the "exact" key word for the '/items' route.
      <Route path='/items' component={Items} />
    </Switch>
  </>
);
```
**Create an `ItemForm.jsx`**
`ItemForm.jsx` will be used to create and edit Items, so it can be create in the `shared` directory.
```js
import React from 'react';
import { Link } from 'react-router-dom';

const ItemForm = ({ item, handleSubmit, handleChange, cancelPath }) => {
    console.log('ItemForm', item)
  
  return (
    <form onSubmit={handleSubmit}>
        <label>Title</label>
        <input
        placeholder='add a title'
        value={item.title}
        name="title"
        onChange={handleChange}
        />

        <label>Link</label>
        <input
        placeholder='add a link'
        value={item.link}
        name="link"
        onChange={handleChange}
        />

        <button type="submit">Submit</button>
        <Link to={cancelPath}>
        <button>Cancel</button>
        </Link>
    </form>
    )
}

export default ItemForm;
```

**In `ItemEdit.jsx`, use the ItemForm:**
```js
import React, { useState, useEffect } from 'react';
import { Redirect } from 'react-router-dom';
import axios from 'axios';

import ItemForm from '../shared/ItemForm';
import Layout from '../shared/Layout';

const ItemEdit = (props) => {
  console.log('ItemEdit - props', props)
  const [item, setItem] = useState({title: '', link: ''})
  const [isUpdated,setIsUpdated] = useState(false)

  useEffect( () => {
       const makeAPICall = async () => {
      try {
          const response = await axios(`http://localhost:3001/api/items/${props.match.params.id}`)
          setItem({ 
            item: response.data.item 
          })
        } catch (err) {
          console.error(err)
        }
  
   }
  makeAPICall()

  }, [])

  const handleChange = event => {
        setItem({
          ...item,
          // use spred operator to get the content rather than the array or object structure
          [event.target.name]: event.target.value
        })
    }

    const handleSubmit = event => {
        event.preventDefault()

        axios({
            url: `http://localhost:3001/api/items/${props.match.params.id}`,
            method: 'PUT',
            data: item
        })
            .then(() => setIsUpdated(true))
            .catch(console.error)
    }

        if (isUpdated) {
            return <Redirect to={`/items/${props.match.params.id}`} />
        }

        return (
            <Layout>
                <ItemForm
                    item={item}
                    handleChange={handleChange}
                    handleSubmit={handleSubmit}
                    cancelPath={`/items/${props.match.params.id}`}
                />
            </Layout>
        )
}

export default ItemEdit;
```

**Update `App.js`:**
```js
import ItemEdit from './components/routes/ItemEdit';
// (...)
    <Switch>
        <Route exact path='/' component={Home} />
        <Route path="/items/:id/edit" component={ItemEdit} />
        <Route path='/items/:id' component={Item} />
        <Route path='/items' component={Items} />
    </Switch>
```
Editing the item should render:
![EditItem](https://i.imgur.com/05rPHIY.png)

**Lastly, in `CreateItem.jsx`:**

```js
import React, { useState } from "react";
import axios from "axios";
import ItemForm from "../shared/ItemForm";
import Layout from "../shared/Layout";
const ItemCreate = (props) => {
    console.log('ItemCreate props', props)
  const [input, setInput] = useState({ title: "", link: "" });
  const [item, setItem] = useState(null);
  const handleChange = (event) => {
    console.log("event", event.target.name, event.target.value);
    setInput({
      ...input,
      [event.target.name]: event.target.value,
    });
  };
  const handleSubmit = (event) => {
    event.preventDefault();
    console.log("handleSubmit");
    axios({
      url: `http://localhost:3001/api/items`,
      method: "POST",
      data: input,
    })
      .then((res) => {
          setItem({ createdItem: res.data.item })
          props.history.push('/items')
        })
      .catch(console.error);
  };
  return (
    <Layout>
      <ItemForm
        item={input}
        handleChange={handleChange}
        handleSubmit={handleSubmit}
        cancelPath="/"
      />
    </Layout>
  );
};
export default ItemCreate;
```

**Update `App.js`:**
In the end, it should look like this:

```js
import React from 'react';
import {Route, Switch, withRouter} from 'react-router-dom';
import Home from './Components/routes/Home';
import Items from './Components/routes/Items';
import Item from './Components/routes/Item';
import ItemEdit from './Components/routes/ItemEdit';
import ItemCreate from './Components/routes/ItemCreate';
import './App.css';

function App() {
  return (
    <>
      <Switch>
        <Route exact path='/' component={Home} />
        <Route path="/items/:id/edit" component={ItemEdit} />
        <Route path='/items/:id' component={Item} />
        <Route path='/items' component={Items} />
        <Route path='/create-item' component={ItemCreate} />
      </Switch>
    </>
  )
};

export default withRouter(App);
```