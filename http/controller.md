# Controllers

## Overview
Controllers are first class objects in a Sand application. They are responsible for responding to requests from a web browser, mobile application or any other system capable of communicating with a server. They often act as a middleman between your models and [views](views.md). For many applications, the controllers will contain the bulk of your project’s [business logic](https://en.wikipedia.org/wiki/Business_logic).

## Actions
Controllers are comprised of a collection of methods called *actions*. Action methods can be bound to routes in your application so that when a client requests the route, the bound method is executed to perform some business logic and (in most cases) generate a response. For example, the `GET /hello` route in your application could be bound to a method like:

```js
static *hello() {
	this.send('Hello World');
}
```
so that any time a web browser is pointed to the `/hello` URL on your app's server, the page displays the message *"Hello World"*.

### Before
*Before* methods are special actions that **always** get called **before** your action, hence the name. You can define a before method like so:

```js
static *before() {}
```
If your *controller* extends a parent then you can call the parents *before* with a: 

```js
yield super.before();
```
Its important to note that your action will be called as soon as the child before method returns. So make sure that if you have *asynchronous* checks to make sure that you `yield` them, otherwise your action may be called *prematurely*  Now there are a couple of helper methods to get around this fact.

#### this.exit()
Calling `this.exit()` will immediately exit your before and **not** call your *action*. Use this aftere you have already sent the *response* in your before and want to exit early.

#### this.skip()
Calling `this.skip()` will skip **all** remaining before functions and go straight into executing your action.  This is useful for when you have made a decision in an early *before* like a base controller and don't want to continue up the before chain.

## Where are Controllers defined?
By default they are defined in the `controllers/` folder. This can be changed with the `http.controllerPath` config option. By convention Sand controllers are [Pascal Cased](http://c2.com/cgi/wiki?PascalCase), so that every word in the filename (including the first word) is capitalized: for example, `User.js`, `MyController.js`, `SuperAwesomeController.js` are all valid Pascal Cased names.

You may organize your controllers into groups by saving them in subfolders of `controllers`, however note that the subfolder name will become part of the Controller’s identity when used for routing (more on that in the "[Routing]()" section).

## What do Controllers look like?

A controller file defines a javascript [class](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) where the *static* methods are *actions*. Here is a simple example of a full controller file.

```js
"use strict";

const Controller = require('sand-http').Controller;

module.export = class extends Controller {
	static *hi() {
		this.send('Hello World');
	}
};
```
Now it may be weird to see that `this` in a *static* function.  All actions are bound to a [Context]() object. For those familiar with [express.js](http://expressjs.com/), the context object contains many of the functions used with express, since sand is built on top of express.

## "Thin" Controllers
Most MVC frameworks recommend writing *"thin"* controllers, and while Sand is no exception (it is a good idea to keep your Sand controllers as simple as possible) it is also helpful to understand "why?"

Controller code is inherently dependent on some sort of trigger or event. In a backend framework like Sand, this event is almost always an incoming request. So if you write a bunch of code in one of your controller actions, it is not uncommon for that code's scope to be dependent on the "request context". Which is fine...until you want to use that code from a slightly different action, or from the command line.

So the goal of the "thin controller" philosophy is to encourage decoupling of reusable code from any related scope entanglements. In Sand, you can achieve this in a number of different ways, but the most common strategies for extrapolating code from controllers are:
	
* Write a custom model method to encapsulate some code that performs a particular task relating to a particular model
*  Write a service as a function to encapsulate some code that performs a particular application-specific task
*  If you find some code which is useful across multiple different applications (and you have time to do this), you should extract it into a node module. Then you can share it across your organization, use it in future projects, or better yet, publish it on [npm](http://npmjs.org) under a permissive open-source license for other developers to use and help maintain.