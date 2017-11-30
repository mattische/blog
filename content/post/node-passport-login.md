---
title: "Node Passport Login"
date: 2017-11-27T13:44:13+01:00
author: "mattische"
tags: [ "node", "passport", "oauth2", "web-development" ]
---


### nodejs with passport

Creating a web application often involves some sort of login and user management.
This is a short example of using nodejs with passport's built in strategies for local (with mongodb) and google OAuth2.<br>

Passport, <a href="http://www.passportjs.org/">http://www.passportjs.org/</a>, have strategies for almost every provider there is. Very handy.<br><br>
I won't list all source code, you'll find that in the Github repo. Instead, some highlights are shown below.<br>

<a href="https://github.com/mattische/node-passport-example">Here is the Github repo</a> with complete sourcecode.<br>


Here is some slides from a workshop I did (in Swedish only); <a href="https://docs.google.com/presentation/d/129y8zoBR6HGa5gQf-DCNoqmLoCEeai8KOsdg7xBo1go/edit?usp=sharing">nodejs + passport</a>


<br><br>

#### Prerequisites
+ <strong>Set up Google</strong><br />
Go to your Google <a href="https://console.developers.google.com">cloud console</a> and create a new project. <br>
After project creation, go to the dashboard in the <code>APIs & services</code> view. Choose to enable APIs and find and enable the Google+ API.<br><br>
Now, in the left pane, choose 'Credentials' and create credentials for this app we are going to create. Choose web application and choose a name.<br>
Enter the origin and callback URIs and don't forget the port if you are using that.<br>
In the 'OAuth consent screen' (the view presented to the user during authentication with google) it is important to use the same name as we did in the previous step (under the Credentials tab). Did'nt work for me with different names<br>
Now you're done. It might take a few minutes for the settings to take effect.


+ <strong>npm init</strong><br>
Setup your local nodejs app with a package.json. Here is the modules I used;<br>
<code> bcrypt body-parser connect-flash cookieparser ejs express express-session flash mongoose morgan passport passport-google-oauth passport-local</code>
<br>
You might want to copy those and do a<br><code>$ npm install &lt;paste modules&gt; --save</code><br>


+ <strong>mongodb</strong><br>
Either install and use a local mongodb instance or use for example a free sandbox mongo-instance from <a href="https://mlab.com">mLab</a>.


+ <strong>layout</strong><br>
The app folder structure is as follows;
<pre>
/config/<br>
      |---- google.js #google configuration with keys and redirect callback
      |---- mongodb.js #mongodb connection string<br>
/models/<br>
      |---- user.js #user-model for mongodb, enabling user creation in db. for local strategy<br>
/passport/
      |---- google.js #passport routes and callbacks for google strategy
      |---- local.js #routes and logic for local strategy<br>
/routes/
      |---- routes.js #routes for home, login, signup, profile etc<br>
/views/      #views with ejs
      |---- index.ejs
      |---- login.ejs
      |---- profile.ejs
      |---- signup.ejs<br>
app.js  #entry point
</pre>



#### app.js
Here is some of the source for the entry point. Nothing really special, but we have separated passport logic into modules which we import.<br>

The local strategy is located in /passport/local.js and google startegy in /passport/google.js.<br>
We pass the 'passport'-object to those - see last line.<br>

Passport needs session to keept track of users.<br>
We do the mandatory call to initialize().
<pre>

var passport = require('passport');

require('./passport/local')(passport); 

require('./passport/google')(passport); 

app.use(session({ secret: "aVerySecretSecret" }));
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser()); 

app.use(passport.initialize());

app.use(passport.session());

require('./routes/routes.js')(app, passport); 


</pre>
<br>



#### session serialization and deserialization
To keep track of user passport needs to serialize (and deserialize) the session.<br>
In /passport/local.js and /passport/google.js we find;
<pre>
passport.serializeUser(function(user, done) {
        done(null, user.id);
    });

    passport.deserializeUser(function(id, done) {
        User.findById(id, function(err, user) {
            done(err, user);
        });
    });
</pre>

<strong>user.id</strong> is saved to <code>session req.session.passport.user = {id:'..'}</code><br>
Which is transfered to parameter <strong>id</strong> in <code>User.findById</code>.<br>
In the done-function the user-parameter, user object attaches to the request as <strong>req.user</strong><br>

#### done - what is it?
Let's repeat the code above;
<pre>
passport.serializeUser(function(user, done) {
        done(null, user.id);
    });

    passport.deserializeUser(function(id, done) {
        User.findById(id, function(err, user) {
            done(err, user);
        });
    });
</pre>

What exactly does the done function do?<br>
From <a href="https://stackoverflow.com/questions/32153865/what-is-done-callback-function-in-passport-strategy-configure-use-function">StackOverflow</a>;
<strong>done</strong> is a method called internally by the strategy implementation.
See <a href="https://github.com/jaredhanson/passport-local/blob/master/lib/strategy.js#L80">here</a>.<br>
There are three possible parameters; err, user, info. In brief;<br>
<strong>err</strong> is for errors, <strong>user</strong> is data attached back to the current request and <strong>info</strong> can be used for messages, such as flash-messages.
<br>
In the above example, in the function serializeUser, no (null) error  object is present and the second argument is data, that in this case, will be serialized. The data is the unique
session id.<br>
In the function deserialzeUser no error object is present and the second argument, user, is the data fetched from database. Passport will attach that user data (unique id) to the session.
<br>


#### routes
The routes are separated from app.js - they are located in /routes/routes.js.<br>
No rocket science going on there, but we wrap it as a module and make sure an instance of the app (express) and a passport object is passed in;<br>
<code>
module.exports = function(app, passport) {

    ... all routes ...

};
</code>
<br>

And, in the end of the file we supply a function to check whether a user is logged in;<br>
<pre>
function isLoggedIn(req, res, next) {    

        if (req.isAuthenticated())
            return next();

        //not logged in
        res.redirect('/');
    }
</pre>


If the user is logged in, we call next(), which will carry on execution.
<br>
If the user is not logged in, we redirect them to index/home. Otherwise nex() is returned, which causes the next middleware to execute.
<br>



#### Protecting views
The function <code>isLoggedIn</code> in /routes/routes.js can be used to check whehter a user is logged in or not. See implementation above<br>
<br>

It is used like this (in the /example route), as a callback;
<pre>
app.get('/example', isLoggedIn, function(req, res) {
            res.render('example.ejs', {
                user : req.user 
            });
        });
</pre>
<br>

The second parameter is <code>isLoggedIn</code> which of course is a callback. It will execute with the request.<br>
