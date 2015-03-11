Over the past few weeks I have created a few Node.js web apps (such as [Bourbon Tweet Alerts](http://bourbontweetalerts.cfapps.io "Bourbon Tweet Alerts")) and I've noticed that there is a huge inconsistency in building Node.js web apps. I desperately needed a way to consistently build projects the same way every time. As we all know, there's nothing like having to figure out how someone made a piece of code function, much less an entire framework! I've been working with Ruby on Rails for a while, so making a transition into Node.js was quite a different leap. The one great thing I like about Rails is standardization. When you create a new rails app, the MVC architecture is templated out for you (ie "batteries included"). Node.js on the other hand, allows you to pick and choose any libraries you want to make the project successful. I've read over countless blogs and not a single one covers everything you need to know how to build a CRUD + REST application from soup to nuts. So I must thank about 20 different blogs and loads of github README's to help assemble this beacon of guidance.

I'll admit it, I'm a sucker for simple generation of skeleton. That's why I loved using rails. The entire MVC structure is laid out for me and I can be on my way. 

```
rails new myapp --database=postgresql
rails generate scaffold Blob name:string badge:integer dob:date isloved:boolean
```

## Generate the Express.js Skeleton

Within Node.js, the standard for creating web apps is [Express.js](http://expressjs.com/ "Express.js"). Express even has a template generator. Since you know I'm a sucker for it, let's use that. I'm going to assume you have [Node.js & NPM already installed](http://nodejs.org/download/ "Node.js Downloads").

```shell
npm install -g express
npm install -g express-generator
```

Great. Now we've got Express & the generator installed as a global command on our system.  To create a skeleton site go to your projects directory. When we create our application, a new folder with all the associated pieces are going to be created with it. By default this will use [Jade](http://jade-lang.com "Jade") as the html templating engine. There are other templating engines such as [EJS](http://www.embeddedjs.com/ "EJS"), [Mustache](https://mustache.github.io/ "Mustache.js") and [Hogan](http://twitter.github.io/hogan.js/ "Hogan"), but once you start understanding Jade, it's very clean for simple web apps. Change out `nodewebapp` with whatever you want to call your application

```shell
express nodewebapp
```

Yep, it was that easy. You should now see these lines start scrolling:
```shell
   create : nodewebapp
   create : nodewebapp/package.json
   create : nodewebapp/app.js
   create : nodewebapp/public
   create : nodewebapp/public/images
   create : nodewebapp/public/stylesheets
   create : nodewebapp/public/stylesheets/style.css
   create : nodewebapp/routes
   create : nodewebapp/routes/index.js
   create : nodewebapp/routes/users.js
   create : nodewebapp/public/javascripts
   create : nodewebapp/views
   create : nodewebapp/views/index.jade
   create : nodewebapp/views/layout.jade
   create : nodewebapp/views/error.jade
   create : nodewebapp/bin
   create : nodewebapp/bin/www

   install dependencies:
     $ cd nodewebapp && npm install

   run the app:
     $ DEBUG=nodewebapp:* ./bin/www
```

## Setup MongoDB

Do you have [MongoDB](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/ "MongoDB") setup? If you haven't, no worries. It's actually incredibly easy with your Mac and [Homebrew](http://brew.sh/ "Homebrew").

```shell
brew update
brew install mongodb
```

Let's start our MongoDB server. Side Note: I prefer to use [iTerm2](http://iterm2.com/ "iTerm2") to have multiple tabs and windows with terminal prompts. From the terminal type `mongod` and you will see Mongo start up. By default, it will always use port 27017.

```shell
kcoleman-mbp: kcoleman$ mongod
mongod --help for help and startup options
2015-03-04T09:59:26.150-0500 [initandlisten] MongoDB starting : pid=13761 port=27017 dbpath=/data/db 64-bit host=kcoleman-mbp.local
2015-03-04T09:59:26.150-0500 [initandlisten] db version v2.6.7
2015-03-04T09:59:26.150-0500 [initandlisten] git version: nogitversion
2015-03-04T09:59:26.150-0500 [initandlisten] build info: Darwin minimavericks.local 13.4.0 Darwin Kernel Version 13.4.0: Sun Aug 17 19:50:11 PDT 2014; root:xnu-2422.115.4~1/RELEASE_X86_64 x86_64 BOOST_LIB_VERSION=1_49
2015-03-04T09:59:26.150-0500 [initandlisten] allocator: tcmalloc
2015-03-04T09:59:26.150-0500 [initandlisten] options: {}
2015-03-04T09:59:26.151-0500 [initandlisten] journal dir=/data/db/journal
2015-03-04T09:59:26.152-0500 [initandlisten] recover : no journal files present, no recovery needed
2015-03-04T09:59:26.184-0500 [initandlisten] waiting for connections on port 27017
```

From another terminal window, lets [create a new database](http://docs.mongodb.org/manual/tutorial/getting-started/ "Getting Started with MongoDB"). We need to use the mongo client and 1 command to create it. Type `mongo` to enter the client/shell and `use dbname`. Of course change `dbname` to whatever name you want to call it. Here is the shell output.

```shell
kcoleman-mbp:kcoleman$ mongo
MongoDB shell version: 2.6.7
connecting to: test
> use nodewebappdb
switched to db nodewebappdb
> db
nodewebappdb
> show dbs
admin               (empty)
local               0.078GB
nodews4             0.078GB
nodewebappdb        0.078GB
>
```

## Setup Mongoose to MongoDB

We will need to install the dependencies before we fire up the server, but I like to make sure we get our database pieces sorted first. Open up your favorite editor, such as [Sublime Text](http://www.sublimetext.com/ "Sublime Text") or [Atom](https://atom.io/ "Atom"), and make a new folder within nodewebapp called `model`. Inside this folder create a new file called `db.js`. This is the file where we are going to place our database connections.

```javascript,linenums=true
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/nodewebappdb');
```

Next open up `app.js` and lets add our `db.js` file to the variables. by adding the line `var db = require('./model/db');`

```javascript,linenums=true
var express = require('express'),
    path = require('path'),
    favicon = require('serve-favicon'),
    logger = require('morgan'),
    cookieParser = require('cookie-parser'),
    bodyParser = require('body-parser'),
    db = require('./model/db'),
    routes = require('./routes/index'),
    users = require('./routes/users');
..
..
..
```

## Install Dependencies
Now we need to install our dependencies. from the terminal of the working directory of `nodewebapp` type

```shell
npm install
```

and you should see a big output

```shell
kcoleman-mbp:nodewebapp kcoleman$ npm install
debug@2.1.2 node_modules/debug
└── ms@0.7.0

cookie-parser@1.3.4 node_modules/cookie-parser
├── cookie-signature@1.0.6
└── cookie@0.1.2

morgan@1.5.1 node_modules/morgan
├── basic-auth@1.0.0
├── depd@1.0.0
└── on-finished@2.2.0 (ee-first@1.1.0)

serve-favicon@2.2.0 node_modules/serve-favicon
├── ms@0.7.0
├── fresh@0.2.4
├── parseurl@1.3.0
└── etag@1.5.1 (crc@3.2.1)

body-parser@1.10.2 node_modules/body-parser
├── media-typer@0.3.0
├── bytes@1.0.0
├── raw-body@1.3.2
├── depd@1.0.0
├── qs@2.3.3
├── iconv-lite@0.4.6
├── on-finished@2.2.0 (ee-first@1.1.0)
└── type-is@1.5.7 (mime-types@2.0.9)

express@4.11.2 node_modules/express
├── utils-merge@1.0.0
├── methods@1.1.1
├── cookie@0.1.2
├── fresh@0.2.4
├── range-parser@1.0.2
├── merge-descriptors@0.0.2
├── cookie-signature@1.0.5
├── escape-html@1.0.1
├── vary@1.0.0
├── media-typer@0.3.0
├── parseurl@1.3.0
├── finalhandler@0.3.3
├── serve-static@1.8.1
├── content-disposition@0.5.0
├── path-to-regexp@0.1.3
├── depd@1.0.0
├── on-finished@2.2.0 (ee-first@1.1.0)
├── qs@2.3.3
├── etag@1.5.1 (crc@3.2.1)
├── proxy-addr@1.0.6 (forwarded@0.1.0, ipaddr.js@0.1.8)
├── accepts@1.2.4 (negotiator@0.5.1, mime-types@2.0.9)
├── type-is@1.5.7 (mime-types@2.0.9)
└── send@0.11.1 (destroy@1.0.3, ms@0.7.0, mime@1.2.11)

jade@1.9.2 node_modules/jade
├── character-parser@1.2.1
├── void-elements@2.0.1
├── commander@2.6.0
├── mkdirp@0.5.0 (minimist@0.0.8)
├── with@4.0.1 (acorn-globals@1.0.2, acorn@0.11.0)
├── constantinople@3.0.1 (acorn-globals@1.0.2)
└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
```

We need to add a few more packages to our package.json file as a dependencies. `Mongoose` will be our connection to MongoDB, `body-parser` is used to examine POST calls, and `method-override` is used by express to create DELETE and PUT requests through forms. read about  [method-override](https://github.com/expressjs/method-override)

```
npm install mongoose --save 
npm install body-parser --save
npm install method-override --save
```

Awesome. Now to test it's all working in the terminal type `npm start` and you shouldn't see any errors:
```shell
kcoleman-mbp:nodewebapp kcoleman$ npm start

> nodewebapp@0.0.1 start /Users/kcoleman/Documents/projects/nodewebapp
> node ./bin/www
```

and if you navigate to `http://127.0.0.0:3000` you will see the following screen:

![Express 127.0.0.1](http://kendrickcoleman.com/images/stories/onetime/nodeexpress127.png)



## HOORAY!
We've got a functioning web server that is talking to Mongo. Part 1 is accomplished.

Coming from a Rails background, it was pretty easy to create a new model with all the routes, controllers and views through a simple scaffold. Node.js on the other hand requires most of this to be done manually.

## Create the Model and Schema
Remember that `model` folder we created earlier? We are going to use that folder to form our objects. Create a new file within that folder for whatever your object is going to be. In this case, I'm going to call it `blobs.js`. Use the plural form of the object.

Each `blob` is going to have a Name (string), Badge (number), DOB (date), and IsLoved (boolean). [Mongoose Schema Types](http://mongoosejs.com/docs/guide.html)   

```javascript,linenums=true
var mongoose = require('mongoose');  
var blobSchema = new mongoose.Schema({  
  name: String,
  badge: Number,
  dob: { type: Date, default: Date.now },
  isloved: Boolean
});
mongoose.model('Blob', blobSchema); 
```

Within `app.js` add this to your variables at the top below our `db` variable we added earlier

```
...
db = require('./model/db'),
blob = require('./model/blobs'),
...
```

## Create the Controller
We've now come to a crossroad. Many blogs and tutorials will show the following using the `index.js` file. IMO, that's bad business. Let's leave `index.js` for front page stuff if you want to add it later on like Contact, About, Login, etc. Even if you are making a simple single-page web app, we can change `app.js` to use a different controller for `/`.

Create a new file in the `routes` folder. In this example, I'm going to create a new file called `blobs.js`.

Here's the fun part! We're going to build our entire controller with all the CRUD and REST pieces completely baked in. We are going to take this piece by piece but all of this will go into the `blob.js` file.

Define the packages we will need
```javascript,linenums=true
var express = require('express'),
	router = express.Router(),
	mongoose = require('mongoose'), //mongo connection
	bodyParser = require('body-parser'), //parses information from POST
	methodOverride = require('method-override'); //used to manipulate POST
```

This portion must be placed before we get to our CRUD and REST. This is completely copy and pasted from [method-override](https://github.com/expressjs/method-override). Using `use` will make sure that every requests that hits this controller will pass through these functions.
```javascript,linenums=true
router.use(bodyParser.urlencoded({ extended: true }))
router.use(methodOverride(function(req, res){
  	if (req.body && typeof req.body === 'object' && '_method' in req.body) {
    	// look in urlencoded POST bodies and delete it
    	var method = req.body._method
    	delete req.body._method
    	return method
  	}
}))
```

Our first big part of the CRUD. We are going to build the GET for grabbing all the Blobs from the database to display it and we are also going to build the POST for creating a new blob. 
```javascript,linenums=true
//build the REST operations at the base for blobs
//this will be accessible from http://127.0.0.1:3000/blobs if the default route for / is left unchanged
router.route('/')
    //GET all blobs
	.get(function(req, res, next) {
	    //retrieve all blobs from Monogo
	    mongoose.model('Blob').find({}, function (err, infophotos) {
		  	if (err) {
		  		return console.error(err);
		  	} else {
		  	    //respond to both HTML and JSON. JSON responses require 'Accept: application/json;' in the Request Header
		  		res.format({
		  		    //HTML response will render the index.jade file in the views/blobs folder. We are also setting "blobs" to be an accessible variable in our jade view
					html: function(){
				    	res.render('blobs/index', {
				  			title: 'All my Blobs',
				  			"blobs" : blobs
			  			});
					},
					//JSON response will show all blobs in JSON format
					json: function(){
				    	res.json(infophotos);
					}
				});
		  	} 	
		});
	})
	//POST a new blob
	.post(function(req, res) {
	    // Get values from POST request. These can be done through forms or REST calls. These rely on the "name" attributes for forms
	    var name = req.body.name;
	    var badge = req.body.badge;
	    var dob = req.body.dob;
	    var company = req.body.company;
	    var isloved = req.body.isloved;
        //call the create function for our database
	    mongoose.model('Blob').create({
	    	name : name,
	    	badge : badge,
	    	dob : dob,
	    	isloved : isloved
	    }, function (err, blob) {
		  	if (err) {
		  		res.send("There was a problem adding the information to the database.");
		  	} else {
		  	    //Blob has been created
		  		console.log('POST creating new blob: ' + blob);
		  		res.format({
		  		    //HTML response will set the location and redirect back to the home page. You could also create a 'success' page if that's your thing
					html: function(){
						// If it worked, set the header so the address bar doesn't still say /adduser
				    	res.location("blobs");
				    	// And forward to success page
	            		res.redirect("/blobs");
					},
					//JSON response will show the newly created blob
					json: function(){
				    	res.json(blob);
					}
				});
	      	}
		})
	});
```

We don't want to spend all of our time creating new Blobs through REST calls, so we are going to use this bit of code to wire up a page called `new.jade` that will display a form. We will build the form a bit later when we get to the Views.
```javascript,linenums=true
/* GET New Blob page. */
router.get('/new', function(req, res) {
    res.render('blobs/new', { title: 'Add New Blob' });
});
```

This middleware will be used to do error checking. Basically, making sure that we have legit entries in the database for the `:id`.
```javascript,linenums=true
// route middleware to validate :id
router.param('id', function(req, res, next, id) {
    //console.log('validating ' + id + ' exists');
    //find the ID in the Database
    mongoose.model('Blob').findById(id, function (err, blob) {
        //if it isn't found, we are going to repond with 404
		if (err) {
			console.log(id + ' was not found');
			res.status(404)
			var err = new Error('Not Found');
			err.status = 404;
			res.format({
				html: function(){
					next(err);
			 	},
				json: function(){
			   		res.json({message : err.status  + ' ' + err});
			 	}
			});
		//if it is found we continue on
		} else {
		    //uncomment this next line if you want to see every JSON document response for every GET/PUT/DELETE call
			//console.log(blob);
			// once validation is done save the new item in the req
			req.id = id;
			// go to the next thing
    		next(); 
		} 
	});
});
```

Now we need to GET an individual blob. We will wire this up later with an `edit.jade` form. This example shows how to grab it by ID.
```javascript,linenums=true
//GET the individual blob by Mongo ID
router.get('/:id', function(req, res) {
    //search for the blob within Mongo
	mongoose.model('Blob').findById(req.id, function (err, blob) {
		if (err) {
			console.log('GET Error: There was a problem retrieving: ' + err);
		} else {
		    //Return the blob
			console.log('GET Retrieving ID: ' + blob._id);
			res.format({
			    //HTML response will render the 'edit.jade' template
				html: function(){
				   	res.render('blobs/edit', {
				  		title: 'Blob' + blob._id,
				  		"blob" : blob
			  		});
			 	},
			 	//JSON response will return the JSON output
				json: function(){
			   		res.json(blob);
			 	}
			});
		}
	});
});
```

PUT is used to update the blob in case we need to make changes. This put accepts REST and form POST requests. This looks eerily similar to POST from earlier because we are basically doing the same thing. Take values, assign values, and then update values. 
```javascript,linenums=true
//PUT to update a blob by ID
router.put('/:id', function(req, res) {
	// Get our REST or form values. These rely on the "name" attributes
    var name = req.body.name;
	var badge = req.body.badge;
	var dob = req.body.dob;
	var company = req.body.company;
	var isloved = req.body.isloved;

    //find the document by ID
	mongoose.model('Blob').findById(req.id, function (err, blob) {
	    //update it
		blob.update({
	    	name : name,
	    	badge : badge,
	    	dob : dob,
	    	isloved : isloved
	    }, function (err, blob) {
		  if (err) {
		  	res.send("There was a problem updating the information to the database: " + err);
		  } 
		  else {
		  		//HTML responds by going back to the page or you can be fancy and create a new view that shows a success page.
		  		res.format({
		  			html: function(){
					   	res.redirect("/blobs/" + blob._id);
				 	},
				 	//JSON responds showing the updated values
					json: function(){
				   		res.json(blob);
				 	}
		  		});
	       }
		})
	});
});
```

DELETE is a pretty crucial part of CRUD. As usual, this will be accepted by REST and POSTs from a form
```javascript,linenums=true
//DELETE a Blob by ID
router.delete('/:id', function (req, res){
    //find blob by ID
	mongoose.model('Blob').findById(req.id, function (err, blob) {
		if (err) {
			return console.error(err);
		} else {
		    //remove it from Mongo
			blob.remove(function (err, blob) {
				if (err) {
					return console.error(err);
				} else {
				    //Returning success messages saying it was deleted
					console.log('DELETE removing ID: ' + blob._id);
					res.format({
					    //HTML returns us back to the main page, or you can create a success page
			  			html: function(){
						   	res.redirect("/blobs");
					 	},
					 	//JSON returns the item with the message that is has been deleted
						json: function(){
					   		res.json({message : 'deleted',
					   			item : blob
					   		});
					 	}
			  		});
				}
			});
		}
	});
});
```
Export all of our routes. 
```javascript,linenums=true
module.exports = router;
```
That's IT! We have built a completely functional controller that contains the ability to do all CRUD operations through web pages as well as REST calls that will respond with JSON formatting. pretty awesome!


## Add The Route
`app.js` is the brains for understanding how to get to everything in your app. We need to add our blobs route variable and add our blob route below the main `/` route. This means that our Blob model will be accessed from `http://127.0.0.1:3000/blobs`

```
var routes = require('./routes/index');
var blobs = require('./routes/blobs');
```

```
app.use('/', routes);
app.use('/blobs', blobs);
```
Are you building a single-page web app? you can always change the root to point to blobs by using `app.use('/', blobs);`. Then our blobs are accessed at `http://127.0.0.1:3000`. This may require you to go back to your controller and change some `res.redirection` pieces.

## Add The Views
Now we need to create our views. Create a new folder inside of `views`. In my case, I'm going to call it `blobs`. Reserve the jade files in the root of `views` for something later. We are going to create views for `index`, `new`, and `edit`

All of these views are using `layout.jade` as a building block. Since this layout lives in a folder one directory up, we preface it with `../`. The result will be `extends ../layout`. If you don't, then you will encounter an error that the app can't find `layout.jade`.

Create a new `index.jade` file within the `blobs` folder. This index file is what is used to show all blobs. Yes, this is ugly but it will atleast give us a look at how it all functions. The `form` allows us to delete the document via a button using a POST operation. The Edit link will take us to another page using GET where we can edit information

```jade
extends ../layout

block content
    h1.
         {#title}
    ul
        - each blob, i in blobs
            li
                = blob.name
                = blob.badge
                = blob.dob
                = blob.isloved
                = blob._id
                form(action='/blobs/#{blob._id}',method='post',enctype='application/x-www-form-urlencoded')
                    input(type='hidden',value='DELETE',name='_method')
                    button(type='submit').
                        Delete
                a(href='/blobs/#{blob._id}') Edit
```

As you can probably guess, we still need a way to add Blobs via a form. Create a new file called `new.jade`. This page is accessible from `/blobs/new`.

```jade
extends ../layout

block content
  h1= title
  form#formAddBlob(name="addblob",method="post",action="/blobs")
      input#inputName(type="text", placeholder="ex. John Smith", name="name")
      input#inputBadge(type="number", placeholder="ex. 123456", name="badge")
      input#inputDob(type="date", name="dob")
      input#inputIsLoved(type="checkbox", name="isloved")
      button#btnSubmit(type="submit") submit
```

Lastly, we need a form that shows the pre-populated data and allows us to manipulate it and update it via PUT.
```jade
extends ../layout

block content
    h1.
        Blob #{blob._id}
    form(action='/blobs/#{blob._id}',method='post',name='updateblob',enctype='application/x-www-form-urlencoded')
      input#inputName(type='text', value='#{blob.name}', name='name')
      input#inputBadge(type='number', value='#{blob.badge}', name='badge')
      input#inputDob(type='date', value='#{blob.dob}', name='dob')
      input#inputIsLoved(type='checkbox', name='isloved', checked=('#{isloved.newsletter}'==='true' ? "checked" : undefined))
      input(type='hidden',value='PUT',name='_method')
      button#btnSubmit(type='submit').
        Update
```

Whew... that's it! Congrats! You've officially created an entire CRUD application with all the usual REST calls baked in. Now you can fire up your server with `npm start` and begin adding new Blobs!

Here is what your folder structure should look like:
```
nodewebapp
│   README.md
|   app.js
|   package.json
│
└───bin
    │   www
    │
└───model
    │   blobs.js
    │   db.js
    |
└───node_modules
    │   too many to list
    |   ...
    │
└───public
    ├───images
    ├───javascripts
    ├───stylesheets
    |
└───routes
    |   blobs.js
    │   index.js
    |   ...
    |
└───views
    │   error.jade
    |   index.jade
    |   layout.jade
    └───blobs
        │   edit.jade
        │   index.jade
        |   new.jade
```