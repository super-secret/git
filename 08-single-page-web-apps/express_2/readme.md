# Express Routing

- Extract routes to separate files
- Describe the role of response methods
- Use module.exports and MVC to organize Express app
- Explain what `bodyParser` is used for
- Create an application that responds to JSON requests
- Explain what JSONP is and why we (sometimes) need it.

## Intro

You visit a page.  You click a link.  You fill in a form and press submit.  These are all requests.  Requests that are directed to a server.  That server needs to know how to respond to that request.  We map the request to the appropriate "controller" via routing.  In development, we spend a lot of time and effort mapping one thing to another.

## Request methods (10 min)

- http://expressjs.com/guide/routing.html
Read Routing, Route Methods, and Route Paths.  Stop at Route Handlers.

This should be familiar from the Express lesson.  Node doesn't provide the obvious separation of concerns that Rails provides, but it's still there.  Like Sinatra, we merge the route definition with the controller functionality.


``` javascript
app.METHOD(path, [callback...], callback)
```
- where `app` is an instance of express,
- `METHOD` is an HTTP request method,
- `path` is a path on the server, and
- `callback` is the function executed when the route is matched.

### Exercise: Pair and Share: Write CRUD routes for a Compliment. (15 min)

## We do: Create module and index route

https://github.com/ga-dc/emergency_compliment

```js
// controllers/complimentsController.js

var complimentsController = {
  index: function(req,res){
    res.render("compliments/index.hbs", {compliments:[]});
  }
}
```

```html
<!-- views/compliments/index.hbs -->
<ul>
  {{#each compliments}}
    <li>{{this}}</li>
  {{/each}}
</ul>
```

```js
// index.js

var express = require("express");
var app = express();
var complimentsController = require("./controllers/complimentsController");

app.get("/compliments", complimentsController.index);

app.listen(3000, function(){
  console.log("app listening at http://localhost:3000/");
})
```

## You do: Create show, edit, and update routes (15 min)

## I do: Create model

```js
// index.js
var compliment = require('./models/compliment')
```

```js
// models/compliment

var compliments = [
  "Your instructors love you",
  "High five = ^5",
  "Is it Ruby Tuesday yet?",
  "It's almost beer o'clock",
  "The Force is strong with you"
];

var Compliment = function(){
  
}

Compliment.all = function(){
  return compliments;
}

module.exports = Compliment;
```

```js
// controllers/complimentsController.js
// compliment model is required in ../index.js

var Compliment = require("../models/compliment");

var complimentsController = {
  index: function(req, res){
    res.render('index', {compliments: Compliment.all()});
  }
}

module.exports = complimentsController;
```

> Bonus: show a random color when the page loads

## I Do: Forms, bodyParser, and creating compliments

### res.redirect

We used this in the first exercise.

``` js
// root route
app.get('/', function (req, res){
  res.redirect('/songs');
});
```

## You do: edit compliments

## Break

## Response Methods (20 min)

> Q. What kind of responses have we given so far?
---

A. html and json.


The [docs](http://expressjs.com/guide/routing.html#response-methods) share many more.

### res.json

That `res.json` looks handy.  THe link takes us to http://expressjs.com/4x/api.html.  If you search for
`res.json([body])`, we'll find what we are looking for.

"This method is identical to res.send() with an object or array as the parameter. However, you can use it to convert other values to JSON, such as null, and undefined. (although these are technically not valid JSON)."

## Let's build a compliment api

>People would actually use this. Ask Robin about his million click moment.

## I do: api

http://stackoverflow.com/a/12984730/850825

```js
// index.js

app.use("*.json",function (req, res, next) {
  req.headers.accept = 'application/json';
  next();
});

app.get("/compliments/?:format?", complimentsController.index)
```

```js
// controllers/complimentsController.js

module.exports = {
  index: function(req, res){
    res.format({
      html: function(){
        res.render("compliments/index.hbs",{compliments: Compliment.all()});
      },
      json: function(){
	res.json(Compliment.all());
      }
    }) 
  }
}
```

### You do: Create, update delete routes

### res.jsonp

TODO Link to a file:// index.html and show AJAX cross-domain failure.

## Conclusion

- What is the purpose of routing?
- What are the three ways we can specify a path?
- Name a request method other than GET, POST, PATCH, PUT, or DELETE.
- In node, how do we reference code in other files?

## Resources
- http://expressjs.com/guide/routing.html
- Really like the organization I see in [this tutorial](https://thewayofcode.wordpress.com/2013/04/21/how-to-build-and-test-rest-api-with-nodejs-express-mocha/)
- Looking for more?  We recommend NodeSchool's [ExpressWorks](https://github.com/azat-co/expressworks)


### Express Route Tester

Similarly, let's take a look at the [Express Route Tester](http://forbeslindesay.github.io/express-route-tester/?_ga=1.256234632.2070291842.1433362238).  It can be a handy tool for testing basic Express routes.


## Conclusion

- What action do you expect this route to perform: `app.post('/authors', function() {...}`?
- Explain the `req` and `res` variables in `app.get('/authors', function(req, res){...})`
- Write a root route that redirects to '/authors'.