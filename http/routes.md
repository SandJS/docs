# Routes

## Overview
The most basic feature of any web application is the ability to interpret a request sent to a URL, then send back a response. In order to do this, your application has to be able to distinguish one URL from another.

Like most web frameworks, Sand provides a router: a mechanism for mapping URLs to controller. Routes are rules that tell Sand what to do when faced with an incoming request. There are two main types of routes in Sand: **custom** (or "explicit") and **automatic** (or "implicit").

## Custom Routes
Sand lets you design your app's URLs in any way you like, there are no framework restrictions.

Every Sand project comes with `config/routes.js`, a simple Node.js module that exports an object of custom, or "explicit" **routes**. For example, this routes.js file defines seven routes:

```js
// config/routes.js
module.exports = {
  'GET /signup': 'User.signup',
  'POST /signup': 'User.processSignup',
  'GET /login': 'User.login',
  'POST /login': 'User.processLogin',
  'GET /logout': 'User.logout',
  'GET /me': 'User.profile'
  'GET /user/(?<userId>\\d+)': 'User.profile'
}
```

Each **route** consists of a **path** (on the left, e.g. `GET /me`) and a **target** (on the right, e.g. `User.profile`). The **path** is a URL path and (optionally) a specific [HTTP method](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) (Defaulting to `GET`). The target is defined as a `Controller.action`. When Sand receives an incoming request, it checks the **path** of all custom routes for matches. If a matching route is found, the request is then passed to its **target**.

For example, we might read `GET /me`: 'User.profile' as:

> "Hey Sand, when you receive a GET request to http://mydomain.com/me, run the profile action of User, would'ya?"

### Notes

* Just because a request matches a route address doesn't necessarily mean it will be passed to that route's *target* directly. For instance, HTTP requests will usually pass through some middleware first. And if the route points to a controller action, the request will need to pass through any before methods first.

## Automatic Routes
In addition to your custom routes, Sand binds many routes for you automatically. If a URL doesn't match a custom route, it may match one of the automatic routes and still generate a response. The main types of automatic routes in Sand are:

* [Controller](controller.md) routes, which provide ease routes matching with the following pattern `/Controller/action`, where *Controller* is the path to the Controller file and and *action* is the action name.
* Assets, such as images, Javascript and stylesheet files.

