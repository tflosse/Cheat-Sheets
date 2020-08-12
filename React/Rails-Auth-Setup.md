## Basic Auth setup Rails to React

Start by creating a new react app and installing the dependencies we will be using or ones you prefer:

```js
npx create-react-app client-app
npm i react-router
npm i react-router-dom
npm i axios 
// for api calls

// you might also choose to use redux and react-redux and other libraries
```

```js
mkdir components
touch Home.js Dashboard.js
touch auth/Registration.js
// will automatically create an auth directory
```

`App.js`
```js
import React, { Component } from 'react';
import { BrowserRouter, Switch, Route } from 'react-router-dom';
import axios from 'axios';
import './App.css';
import Home from './components/shared/Home';
import Dashboard from './components/routes/Dashboard';

export default class App extends Component {
  render() {
    return (
      <div className='App'>
        <BrowserRouter>
          <Switch>
            <Route exact path={'/'} component={Home} />
            <Route exact path={'/dashboard'} component={Dashboard} />
          </Switch>
        </BrowserRouter>
      </div>
    );
  }
}
```

`Home.js`
```js
import React, { Component } from 'react'
import Registration from '../auth/Registration'

export default class Home extends Component {
    render() {
        return (
            <div>
                <h1>Home</h1>
                // <Registration />
                // Will be added later
            </div>
        )
    }
}
```

`Dashboard.js`
```js
import React from 'react'

function Dashboard() {
    return (
        <div>
            <div>
                <h1>Dashboard</h1>
            </div>
        </div>
    )
}

export default Dashboard;
```

Now we can start working on a registration form:
`Registration.js`

If you are using class components, you might want to read up on "this." and "state"
```js
import React, { Component } from 'react';

export default class Registration extends Component {
    constructor(props) {
        super(props);

        this.state = {
            email: "",
            password: "",
            password_confirmation: "",
            registrationErrors: ""
        }
        this. //tie in hanfler functions here.
    }
    render() {
        return (
            <div>
                Registration goes here.
            </div>
        )
    }
}
```

### Registration Page:
But we will be using functional components and state hooks:
```js
import React, { Component, useState } from "react";

export default function Registration() {
  const [registration, setRegistration] = useState({
    email: "",
    password: "",
    password_confirmation: "",
    registrationErrors: "",
  });

  const handleSubmit = (event) => {
    console.log("Registration form submitted.");
    event.preventDefault();
  };

  const handleChange = (event) => {
    console.log("Handling registration input change.", event);
    setRegistration({
        ...registration,
    [event.target.name]: event.target.value});
  };

  return (
    <div>
      <h3>Sign up</h3>
      <form onSubmit={handleSubmit}>
        <input
          type="email"
          name="email"
          placeholder="Email"
          value={registration.email}
          onChange={handleChange}
          required
        ></input>
        <input
          type="password"
          name="password"
          placeholder="Password"
          value={registration.password}
          onChange={handleChange}
          required
        ></input>
        <input
          type="password"
          name="password_confirmation"
          placeholder="Confirm Password"
          value={registration.password_confirmation}
          onChange={handleChange}
          required
        ></input>
        <button type="submit">Register</button>
      </form>
    </div>
  );
}
```

Now, we want to make the POST api call:
```js
  const handleSubmit = (event) => {
    console.log("Registration form submitted.");
    axios.post("http://localhost:3000/registrations", {
        user: {
            username: registration.username,
            email: registration.email,
            password: registration.password,
            password_confirmation: registration.password_confirmation
        }
    }, 
    { withCredentials: true }
    // Above is absolutely critical to create the cookie and set it inside the browser.
    )
    .then((response) => {
        console.log("Registration response -", response);
      })
      .catch((error) => {
        console.log("Registration error -", error);
      });
    event.preventDefault();
  };
```

Test it out and double-check on Rails Console:
![](https://i.imgur.com/OwT5iIg.png)
![](https://i.imgur.com/PUYCgkH.png)
![](https://i.imgur.com/t4B9MD4.png)
![](https://i.imgur.com/fnfRZir.png)

### Passing user as props

In `App.js`, create a state and use render props:
```js
export default function App() {

  const [currentUser, setCurrentUser] = useState({
    loggedInStatus: "NOT_LOGGED_IN",
    user: {}
  });

    return (
      <div className='App'>
        <BrowserRouter>
          <Switch>
            <Route 
            exact path={'/'} 
            render={props => (
              <Home {...props} loggedInStatus={currentUser.loggedInStatus} />
            )} />
            <<Route exact 
            path={'/dashboard'} render={props => (
              <Dashboard {...props} loggedInStatus={currentUser.loggedInStatus} />
            )} />
          </Switch>
        </BrowserRouter>
      </div>
    );
};
```

In `home.js` and 'Dashboard.js`, we'll passed the `loggedInStatus` as props:
```js
import React from 'react';
import Registration from '../auth/Registration';

export default function Home(props) {

    return (
        <div>
            <h1>Home</h1>
            <h1>Status: {props.loggedInStatus} </h1>
            <Registration />
        </div>
    )
};
```

```js
import React from 'react';

export default function Dashboard(props) {
    return (
        <div>
            <div>
                <h1>Dashboard</h1>
                <h2>Status: {props.loggedInStatus}</h2>
            </div>
        </div>
    )
};
```

We'll pass up the registration event to App.
In Registration, we change the axios promise to:
```js
.then((response) => {
          if (response.data.status === 'created') {
            props.handleSuccessfulAuth(response.data);
          }
        console.log("Registration response -", response);
      })
      .catch((error) => {
        console.log("Registration error -", error);
      });
```

Then pass it to Home:
```js
export default function Home(props) {

        const handleSuccessfulAuth = (data) => {
        // Update parent component
        props.handleLogin(data);
        props.history.push("/dashboard");
    }

    return (
        <div>
            <h1>Home</h1>
            <h2>Status: {props.loggedInStatus} </h2>
            <Registration handleSuccessfulAuth={handleSuccessfulAuth}/>
        </div>
    )
};
```

Pass `handleLogin` from App.js:
```js
const handleLogin = (data) => {
    setCurrentUser({
      loggedInStatus: "LOGGED_IN",
      user: data.user,
    });
  };

  // ...

  <Route
    exact
    path={"/"}
    render={(props) => (
        <Home
        {...props}
        handleLogin={handleLogin}
        loggedInStatus={currentUser.loggedInStatus}
        />
    )}
    />
```
![](https://i.imgur.com/BZc1Yvm.png)

### Handling sign-in and keeping User signed in

Adding a Login.js Component (`touch ...`)

```js
import React, { useState } from "react";
import axios from "axios";

export default function Login(props) {
  const [session, setSession] = useState({
    email: "",
    password: "",
    loginErrors: "",
  });

  const handleChange = (event) => {
    console.log("Handling login input change.", event);
    setSession({
      ...session,
      [event.target.name]: event.target.value,
    });
  };

  const handleSubmit = (event) => {
    console.log("Login attempted.");
    axios
      .post(
        "http://localhost:3000/sessions",
        {
          user: {
            email: session.email,
            password: session.password,
          },
        },
        { withCredentials: true }
      )
      .then((response) => {
        // if (response.data.status === "created") {
        //   props.handleSuccessfulAuth(response.data);
        // }
        console.log("Login response -", response);
      })
      .catch((error) => {
        console.log("Login error -", error);
      });
    event.preventDefault();
  };

  return (
    <div>
      <h3>Sign in</h3>
      <form onSubmit={handleSubmit}>
        <input
          type="email"
          name="email"
          placeholder="Email"
          value={session.email}
          onChange={handleChange}
          required
        ></input>
        <input
          type="password"
          name="password"
          placeholder="Password"
          value={session.password}
          onChange={handleChange}
          required
        ></input>
        <button type="submit">Log in</button>
      </form>
    </div>
  );
}
```

Import to comonent into `Home.js`
```js
import React from 'react';
import Registration from '../auth/Registration';
import Login from '../auth/Login';

export default function Home(props) {

    const handleSuccessfulAuth = (data) => {
        // Update parent component
        props.handleLogin(data);
        props.history.push("/dashboard");
    }

    return (
        <div>
            <h1>Home</h1>
            <h2>Status: {props.loggedInStatus} </h2>
            <Registration handleSuccessfulAuth={handleSuccessfulAuth}/>
            <Login />
        </div>
    )
};
```

![](https://i.imgur.com/gGLxb2V.png)
![](https://i.imgur.com/JuuAxJy.png)
![](https://i.imgur.com/0hbuOCI.png)

`Login.js`
```js
import React, { useState } from "react";
import axios from "axios";

export default function Login(props) {
  const [session, setSession] = useState({
    username: "",
    email: "",
    password: "",
    loginErrors: "",
  });

  const handleChange = (event) => {
    console.log("Handling login input change.", event);
    setSession({
      ...session,
      [event.target.name]: event.target.value,
    });
  };

  const handleSubmit = (event) => {
    console.log("Login attempted.");
    axios
      .post(
        "http://localhost:3000/sessions",
        {
          user: {
            username: session.username,
            email: session.email,
            password: session.password,
          },
        },
        { withCredentials: true }
      )
      .then((response) => {
        if (response.data.logged_in) {
          props.handleSuccessfulAuth(response.data);
        }
        console.log("Login response -", response);
      })
      .catch((error) => {
        console.log("Login error -", error);
      });
    event.preventDefault();
  };

  return (
    <div>
      <h3>Sign in</h3>
      <form onSubmit={handleSubmit}>
        <input
          type="username"
          name="username"
          placeholder="Name"
          value={session.username}
          onChange={handleChange}
          required
        ></input>
        <input
          type="email"
          name="email"
          placeholder="Email"
          value={session.email}
          onChange={handleChange}
          required
        ></input>
        <input
          type="password"
          name="password"
          placeholder="Password"
          value={session.password}
          onChange={handleChange}
          required
        ></input>
        <button type="submit">Log in</button>
      </form>
    </div>
  );
}
```

```js
import React from 'react';
import Registration from '../auth/Registration';
import Login from '../auth/Login';

export default function Home(props) {

    const handleSuccessfulAuth = (data) => {
        // Update parent component
        props.handleLogin(data);
        props.history.push("/dashboard");
    }

    return (
        <div>
            <h1>Home</h1>
            <h2>Status: {props.loggedInStatus} </h2>
            <Registration handleSuccessfulAuth={handleSuccessfulAuth} />
            <Login handleSuccessfulAuth={handleSuccessfulAuth} />
        </div>
    )
};
```

```js
import React, { useState } from "react";
import { BrowserRouter, Switch, Route } from "react-router-dom";
// import axios from 'axios';
import "./App.css";
import Home from "./components/shared/Home";
import Dashboard from "./components/routes/Dashboard";

export default function App() {
  const [currentUser, setCurrentUser] = useState({
    loggedInStatus: "NOT_LOGGED_IN",
    user: {},
  });

  const handleLogin = (data) => {
    setCurrentUser({
      loggedInStatus: "LOGGED_IN",
      user: data.user
    });
  };

  return (
    <div className="App">
      <BrowserRouter>
        <Switch>
          <Route
            exact
            path={"/"}
            render={(props) => (
              <Home
                {...props}
                handleLogin={handleLogin}
                loggedInStatus={currentUser.loggedInStatus}
              />
            )}
          />
          <Route
            exact
            path={"/dashboard"}
            render={(props) => (
              <Dashboard
                {...props}
                loggedInStatus={currentUser.loggedInStatus}
              />
            )}
          />
        </Switch>
      </BrowserRouter>
    </div>
  );
}
```

### Keeping the user logged in:

`App.js`
```js
  useEffect(() => {
    const checkLoginStatus = async () => {
      await axios
        .get("http://localhost:3000/logged_in", { withCredentials: true })
        .then((response) => {
          console.log("Logged in? ", response);
          if (
            response.data.logged_in &&
            currentUser.loggedInStatus === "NOT_LOGGED_IN"
          ) {
            setCurrentUser({
              loggedInStatus: "LOGGED_IN",
              user: response.data.user,
            });
          } else if (
            !response.data.logged_in &&
            currentUser.loggedInStatus === "NOT_LOGGED_IN"
          ) {
            setCurrentUser({
              loggedInStatus: "NOT_LOGGED_IN",
              user: {},
            });
          }
        })
        .catch((error) => {
          console.log("Check login error -", error);
        });
    };

    checkLoginStatus();
  }, []);
```

![]()

### Log users out

In `App.js`:
```js
const handleLogout = () => {
    console.log("Handling logout.")
    axios.delete("http://localhost:3000/logout", { withCredentials: true})
    .then(response => {
      setCurrentUser({
      loggedInStatus: "NOT_LOGGED_IN",
      user: {}
      })
    }).catch(error => {
      console.log("Logout error -", error)
    })
  };
```

In `Home.js`:
```js
import React from 'react';
import Registration from '../auth/Registration';
import Login from '../auth/Login';

export default function Home(props) {

    const handleSuccessfulAuth = (data) => {
        // Update parent component
        props.handleLogin(data);
        props.history.push("/dashboard");
    };
    
    return (
        <div>
            <h1>Home</h1>
            <h2>Status: {props.loggedInStatus} </h2>
            <Registration handleSuccessfulAuth={handleSuccessfulAuth} />
            <Login handleSuccessfulAuth={handleSuccessfulAuth} />
            <button onClick={() => {props.handleLogout()}}>Log out</button>
        </div>
    )
};
```









