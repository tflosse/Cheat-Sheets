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
- ItemCreate.jsx, etc.

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
        const response = await axios(`http://localhost:3000/api/items`)
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


