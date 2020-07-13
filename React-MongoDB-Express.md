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


##### Setting up React 

Install react, Router, and Router-DOM in the `Client` directory of the App (create it if necessary (`mkdir`))
```
npx create-react-app app-name
npm i react-router
npm i react-router-dom
```
> Ports might need to be changed depending on where Express and React are running.

Additionally, it is good practice to create a `Components` directory and have React Components in `Routes` and `Shared` directories where applicable.
ej. Routes:
- Home.jsx
- Item.jsx
- ItemCreate.jsx, etc.
Shared:
- Nav.jsx
- Layout.jsx
- Footer.jsx