# express-router-config
> The laziest way to route your express app.

[![Build Status][travis-image]][travis-url]
[![NPM Version][npm-image]][npm-url]
[![Downloads Stats][npm-downloads]][npm-url]

This is an effort to maximize laziness in routing express app. This tool includes grouping of routes, sorting of routes, and applying multiple middleware in most convenient way, of course the structure is inspired by [react-router-config](https://www.npmjs.com/package/react-router-config).

## Features:

* Sorting - will sort your routes, wildcards has been taken into account too.
* Grouping - will add a prefix to your routes depending on the group.
* Multiple Middleware - will let you add one or more middleware in most convenient way.
* Pre/Post Middleware - let's you define whether the middleware should execute before or after executing your function

## Installation

```
npm install -S express-router-config
```

## Usage

```javascript
import express from 'express'
import router from 'express-router-config'
import { getUserById, getUser, login } from './controllers/users'
import { restrictedRoute } from './middlewares'

/** create express server */
const server = express.createServer()
/** configure your routes */
// NOTE: you may or may not use group, but for the sake of grouping up
// your endpoints it's best to use it.
router(server)([
  {
    group: 'users',
    routes: [
      {
        match: '/:id',
        middleware: restrictedRoute,
        method: 'get',
        action: getUserById
      },
      {
        match: '/',
        method: 'get',
        action: getUser
      }
    ]
  },
  {
    match: '/login',
    method: 'post',
    action: login
  }
])

server.listen(8080)
```

### Setting middleware's execution

You may choose to add middleware after the function's execution, in order to do that you must use the following syntax:

```javascript
import * as express from 'express'
import router from 'express-router-config'
import { getUserById } from './controllers/users'
import { authenticate, logger } from './middlewares'

/** create express server */
const server = express.createServer()
/** configure your routes */
// NOTE: you may or may not use group, but for the sake of grouping up
// your endpoints it's best to use it.
router(server)([
  {
    match: '/users/:id',
    method: 'get',
    middleware: [
      ['before', authenticate],
      ['after', logger]
    ]
    action: getUserById
  }
])

server.listen(8080)
```
in the example above, the `/users/:id` route is authenticated first, then executes `getUserById`, after the execution of the `getUserById` the `logger` is executed right away.

## Why use express-router-config?

You don't have to, but if you're used to `react-router-config`, then this would make it easier for you to configure your routes as
it is almost similar structure to `react-router-config`, another advantage of using this route tool is you can easily chain your middleware. For instance if you want to protect all of your routes, and at same time a single route would require additional middleware, you'd do it like this:

```javascript
import { anotherMiddleware } from './middlewares'

router(server)([
  {
    // assuming that you want to protect all routes under /users
    group: 'users',
    middleware: restrictedRoute,
    routes: [
      {
        match: '/:id',
        middleware: anotherMiddleware,
        method: 'get',
        action: getUserById
      },
      {
        match: '/',
        method: 'get',
        action: getUser
      }
    ]
  }
])
```
the code from above would use `restrictedRoute` middleware first, and if you're going to access /users/:id,
it would also use `anotherMiddleware` middleware.

## Disclaimer

* ~~Didn't have time to write tests, don't expect that things should work the way it should.~~
* Current releases may not be stable until further notice. I didn't have much time to tinker around. Any kind of help is appreciated.

[npm-image]: https://img.shields.io/npm/v/express-router-config.svg?style=flat-square
[npm-url]: https://npmjs.org/package/express-router-config
[npm-downloads]: https://img.shields.io/npm/dm/express-router-config.svg?style=flat-square
[travis-image]: https://travis-ci.org/yakovmeister/express-router-config.svg?branch=dev
[travis-url]: https://travis-ci.org/yakovmeister/express-router-config

## Credits

credits to [@yakovmeister](https://github.com/yakovmeister) for the original idea