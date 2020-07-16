## Useful List of Installs
**Note:** The following commands only apply to Mac OS
*- Additonally, some brew installed required an update to macOS Catalina (v.10.15)*

- Homebrew
    Installation info [here](https://brew.sh/) +     [`Docs`](https://docs.brew.sh/)
    >/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

    Updates might be required: 
    > brew update
    - Git
    > brew install git
    - Command Line Tools
    > xcode-select --install (will need to accept CLT License)
    - Mongo
    > brew tap mongodb/brew <br />
    > brew services start mongodb-community@4.2
    - Heroku CLI
    > brew tap heroku/brew && brew install heroku

- Node.js
    Link to [Download](https://nodejs.org/en/download/) Node.js + [`Docs`](https://nodejs.org/en/docs/)
    *Use npm (node package manager for):*
    - React
    > npx create-react-app `app-name`
    - React Router
    > npm i react-router
    - React Router DOM
    > npm i react-router-dom
    - Node modules
    > npm init `-y` (for default behavior)
    - Express
    > npm i express
    - Nodemon
    > npm i nodemon `-g` (to install globally)
    - Dotenv
    > npm i dotenv
    - Cors
    > npm i cors
    - Mongoose
    > npm i mongoose
    - Morgan
    *(works with Express Server, and with Node.js `http` module)*
    > npm i morgan `--save`
    - Axios
    > npm i axios
    - Surge
    > npm i `--global` surge

- VS Code (Visual Studio)
    - Prettier
    - ES6, etc. Snippets
    - Markdown Preview Enhance
    - Bracket Pair Colorizer
    - Live Server

- Postman
    Link to [Download](https://www.postman.com/downloads/) Postman

- MongoDB Compass
    Link to [Download](https://www.mongodb.com/try/download/compass) MongoDB Compass

- Chrome Extensions
    - JSON Formatter ([Chrome Web Store](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa?hl=en))
    - React Development Tools ([Chrome Web Store](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en))