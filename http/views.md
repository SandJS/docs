# Views

## Overview
In Sand, views are markup templates that are compiled on the server into HTML pages. In most cases, views are used as the response to an incoming HTTP request, e.g. to serve your home page.

## Creating a view
By default, Sand is configured to use EJS (Embedded Javascript) as its view engine. The syntax for EJS is highly conventional- if you've worked with php, asp, erb, gsp, jsp, etc., you'll immediately know what you're doing.

If you prefer to use a different view engine, there are a multitude of options. Sand supports all of the view engines compatible with Express.

Views are defined in your app's `views/` folder by default, but like all of the default paths in Sand, they are [configurable](). If you don't need to serve dynamic HTML pages at all (say, if you're building an API for a mobile app), you can remove the directory from your app.

## Compiling a view
Anywhere you can access the [Context]() object (i.e. controller action, before) you can use `this.render()` to compile one of your views, this automatically sends the HTML down to the user.


## Layouts
When building an app with many different pages, it can be helpful to extrapolate markup shared by several HTML files into a layout. This [reduces the total amount of code](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) in your project and helps you avoid making the same changes in multiple files down the road.

### Creating Layouts
Sand layouts are special `.ejs` files in your app's `views/layout` folder you can use to "wrap" or "sandwich" other views. Layouts usually contain the preamble (e.g. `!DOCTYPE html<html><head>....</head><body>`) and conclusion (`</body></html`). Then the original view file is included using `<%- body %>`. Layouts are never used without a view- that would be like serving someone a bread sandwich.

Layout support for your app can be configured or disabled in `config/http.js`, and can be overridden for a particular route or action by setting special data called `layout`. By default, Sand will compile all views using the layout specified in `http.view.layout`, which is `false` by default.

To specify what layout a view uses, see the example below.

The example controller action below will use the view located at `/views/users/profile.ejs` within the layout located at `/views/layout/users.ejs`

```js
static *profile() {
  this.render('user/profile', {layout: 'users'});
}
```

### Data / Locals
The variables accessible in a particular view are called **data or locals**. Locals represent server-side data that is accessible to your view -- locals are not actually included in the compiled HTML unless you explicitly reference them using special syntax provided by your view engine.

```html
<div>Hello, <a><%= name %></a>.</div>
```

#### Using locals in your views
The notation for accessing locals varies between view engines. In EJS, you use special template markup (e.g. `<%= someValue %>`) to include locals in your views.

There are three kinds of template tags in EJS:

* `<%= someValue %>`
	* HTML-escapes the `someValue` local, and then includes it as a string. 
* `<%- someRawHTML %>`
	* Includes the `someRawHTML` local verbatim, without escaping it.
	* Be careful! This tag can make you vulnerable to XSS attacks if you don't know what you're doing.
* `<% if (!loggedIn) { %> <a>Logout</a> <% } %>`
	* Runs the javascript inside the `<% ... %>` when the view is compiled.
	* Useful for conditionals (`if/else`), and looping over data (`for/each`).

Here's an example of a view (views/users/cheeseProfile.ejs) using two locals, user and cheeses:

```html
<div>
  <h1><%= user.name %>'s Cheeses</h1>
  <h2>My stinky cheese collection:</h2>
  <ul>
    <% for (var cheese of cheeses) { %>
    <li><%= chese.name %></li>
    <% }) %>
  </ul>
</div>
```

Assuming we hooked up our route to one of our controller's actions (and our models were set up), we might send down our view like this:

```js
// in controllers/User.js...

  static *cheeseProfile() {
    // ...
    this.render('users/cheeseProfile', {
      user: theUser,
      cheeses: theUser.cheesesCollection
    });
  }
  
// ...
```

### CSS

* `printCSS`: Accepts an array of css files and loads them from the public/css directory. 
* `css`: Loads all other css files requested with `view.css` 
* `view.css`: Accepts an array of css files from the public/css directory that are required for the template.

###### Example

```ejs
//In main template
<%- printCSS(['lib/bootstrap.min', 'lib/font-awesome.min', 'main'])%>
<%- css %>

//In a template included in main template
<% view.css(['product']) %>
```