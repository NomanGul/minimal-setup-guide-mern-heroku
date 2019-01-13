# minimal-setup-guide-mern-heroku

Hello Beautiful Developers :smiley: This is our minimal setup for mern stack app with Heroku hosting

## Initialize Your Project with npm
```
mkdir mern-heroku
cd mern-heroku
npm init
```
During the init process you will be prompted to add a GitHub repository. Now would be a good time to make a new project on GitHub. Add the repo link to your package.json when prompted.

## Initialize Your Project with Git :octocat:
```
git init
git remote add origin <URL-to-your-repo>
```
Add `node_modules/` to your `.gitignore`.

## Set Up a Simple Express Server :rocket:
```
npm install express --save
```
Add a server.js:
```
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send("Hello World!");
});

const PORT = process.env.PORT || 3500;
app.listen(PORT);
```

:cyclone: Start the server and navigate to localhost:3500 to verify that it works. You don't need to use port 3500, but don't use 3000. That's the default for :atom: Create React App.

## Deploy to Heroku :trollface:
Do not wait until the last minute to deploy! Let’s deploy this now so we can test deployment periodically.

Update package.json:
```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "start": "node server.js"
  },
```
First create an account on Heroku https://www.heroku.com and install heroku CLI. Then log into Heroku via the command line and create a new app:
```
heroku login
heroku create
```

Verify the creation of your Heroku app by running `git remote -v`.

Then run the following:
```
git add .
git commit -m “First”
git push -u origin master
git push heroku master
```
Navigate to the URL provided to verify deployment.

In the future:
```
git add .
git commit -m “Ch-ch-ch-changes…”
git push heroku master
```

## MongoDB :page_facing_up:

Log into mlab.com, create your app db and user (if not created before). Don't worry, it's free.

Under db name, reveal your app config variables. There it is, our MongoDB environment variable, `MONGODB_URI`. You will see how that is used below.

Install Mongoose:
`npm install --save mongoose`

Add to server.js:
```
const mongoose = require('mongoose');

mongoose.connect(
  "mongodb://username:userpassword@ds123456.mlab.com:12345/mern-heroku",
  { useNewUrlParser: true }
);

```
Add `models` and `routes` directories to your app:

`mkdir models routes`

To these add placeholder `index.js` files:

`touch models/index.js routes/index.js`

:pushpin: TODO: Basic API route && Schema demos, but for the time being RTFM: https://github.com/Automattic/mongoose#overview

## React :atom:

Install Create React App if you don't already have it:

`npm install -g create-react-app`

From within your the root directory of your app, run

`create-react-app client`

Now let's connect the front to the back using concurrently and a proxy:

`npm install --save concurrently`

To the package.json in the root directory of your app, add two new scripts:
```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "client": "npm run start --prefix client",
    "dev": "concurrently \"npm run start\" \"npm run client\""
  },
```

To the package.json in your client directory, just below "description", add:

`"proxy": "http://localhost:3500/",`

From here you will start your app from its root directory with:
`npm run dev`

This will start both servers _concurrently_. Try it! :hushed:

## Heroku, Again

Add the path package:

`npm install --save path`

In server.js, replace the 'Hello World!' get route with the following:
```
if (process.env.NODE_ENV === "production") {
  app.use(express.static("client/build"));
  const path = require('path');
  app.get('*', (req, res) => {
    res.sendFile(path.resolve(__dirname, 'client', 'build', 'index.html'));
  });
}
```
All requests will be met with the index.html file as a response. From there, you will use ReactRouter.

To the package.json in the root directory of your app, add one more script:
```
"heroku-postbuild": "NPM_CONFIG_PRODUCTION=false npm install --prefix client && npm run build --prefix client"
```
See also: https://devcenter.heroku.com/articles/nodejs-support#customizing-the-build-process

Add, commit and push to Heroku. Verify that your app builds and is now live.

:man_technologist: Happy Hacking!
