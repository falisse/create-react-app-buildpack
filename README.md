Heroku Buildpack for create-react-app
=====================================

Deploy React.js web apps generated with [create-react-app](https://github.com/facebookincubator/create-react-app).

> Automates deployment with the built-in tooling and serves it up via [Nginx](http://nginx.org/en/).

Requires
--------

* [Heroku](https://www.heroku.com/home)
  * [command-line tools (CLI)](https://toolbelt.heroku.com)
  * [a free account](https://signup.heroku.com)
* [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org)
* [create-react-app](https://github.com/facebookincubator/create-react-app)
  * `npm install -g create-react-app`

Usage
-----

### Generate a React app

```bash
create-react-app my-app
cd my-app
```

### Make it a git repo

```bash
git init
```

### Create the Heroku app

```bash
heroku create -b https://github.com/mars/create-react-app-buildpack.git
```

### Commit & deploy ♻️

```bash
git add .
git commit -m "react-create-app on Heroku"
git push heroku master
```

### Visit the app's public URL in your browser

```bash
heroku open
```

### Visit the Heroku Dashboard for the app

```bash
heroku browse
```

### Continue Development

Work with your app locally using `npm start`. See: [create-react-app docs](https://github.com/facebookincubator/create-react-app#getting-started)

Then, commit & deploy ♻️


Customization
-------------

The web server may be [configured via the static buildpack](https://github.com/heroku/heroku-buildpack-static#configuration).

The default `static.json`, if it does not exist in the repo, is:

```json
{ "root": "build/" }
```

### Routing clean URLs

By default, [React Router](https://github.com/reactjs/react-router) (not included) uses hash-based URLs like `https://example.com/index.html#/users/me/edit`. This is nice & easy when getting started with local development, but for a public app you probably want real URLs like `https://example.com/users/me/edit`.

Create a `static.json` file to configure the web server for clean [`browserHistory` URLs with React Router](https://github.com/reactjs/react-router/blob/master/docs/guides/Histories.md#browserhistory):

```json
{
  "root": "build/",
  "clean_urls": false,
  "routes": {
    "/**": "index.html"
  }
}
```

Version compatibility
---------------------

We'll keep branches to maintain compatibility as `create-react-app` evolves. These will only be useful for projects that have been ejected and therefore stagnate with the tooling of a specific version.

Currently, using branch `cra-0.2.x` will ensure that your deployment continues to work with 0.2.x versions of `create-react-app`.

```bash
heroku create -b https://github.com/mars/create-react-app-buildpack.git#cra-0.2.x
```

Usually, using master [as directed in the main instructions](#create-the-heroku-app) will be appropriate to automatically keep up with the newest `create-react-app`.


Architecture 🏙
------------

This buildpack composes three buildpacks (specified in [`.buildpacks`](.buildpacks)) to support **no-configuration deployment** on Heroku:

1. [`heroku/nodejs` buildpack](https://github.com/heroku/heroku-buildpack-nodejs)
  * complete Node.js enviroment to support the webpack build
  * `node_modules` cached between deployments
2. [`mars/create-react-app-inner-buildpack`](https://github.com/mars/create-react-app-inner-buildpack)
  * generates the [default `static.json`](#customization)
  * performs the production build for create-react-app, `npm run build`
3. [`heroku/static` buildpack](https://github.com/heroku/heroku-buildpack-static)
  * [Nginx](http://nginx.org/en/) web server
  * handy static website & SPA (single-page app) [customization options](https://github.com/heroku/heroku-buildpack-static#configuration)
