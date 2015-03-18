# bigwheel quick start

Here's a very concise example of how to use `bigwheel`. Note this is not the best way to layout your application but it's just a very concise example to show how `bigwheel` works while showing off many features:

```javascript
var bigwheel = require('bigwheel');
var Tween = require('gsap');

// create our framework instance
var framework = bigwheel( function(done) {

  // the function passed to bigwheel should return
  // a setting object or alternately you can pass
  // the setting object to the callback defined as
  // done. This is nice if you need to do assynchronous
  // loading before content should be shown
  return {

    // define our routes
    // routes are associated to "sections"
    // sections are functions or objects
    routes: {
      '/': Section,
      '/about': Section,
      '/contact': Section
    }
  };
});

// this will start bigwheel and it will start resolving routes
framework.init();

// This is the definition for the sections which bigwheel will run
// sections can define init, resize, animateIn, animateOut, destroy functions
// these will methods will be called by bigwheel
function Section() {

  var el;
  
  return {

    // the init function creates the view and initializes it
    // after init finishes the view should not be visible
    init: function(req, done) {
      el = createEl(req);      
      el.onclick = function() {
        framework.go(getToSection(req));
      };
      done();
    },

    // the resize function will be called imediately after init
    // here you can apply "responsive" calculations on your view
    resize: function(width, height) {
      var fontSize = width / 500 * 30;
      el.style.fontSize = fontSize + 'px';
      el.style.top = Math.round(( height - fontSize ) * 0.5) + 'px';
    },

    // in animateIn you'll animate in your hidden content that
    // was created in init
    animateIn: function(req, done) {
      Tween.from(el, 1, {
        y: -100, 
        opacity: 0,
        ease: Back.easeOut, 
        onComplete: done
      });
    },

    // in animateOut you'll animate out your content that
    // was created in init
    animateOut: function(req, done) {
      Tween.to(el, 0.25, {
        y: 100, 
        opacity: 0, 
        ease: Back.easeIn, 
        onComplete: done
      });
    },

    // in destroy you'll clean up the content which was
    // created in init
    destroy: function(req, done) {
      el.parentNode.removeChild(el);
    }
  };
}

// this is just a utility function created for this example to create
// an element which will be added to the dom and initialized
function createEl(req) {
  var el = document.createElement('a');
  el.innerHTML = 'Click to go from "' + req.route + '" to "' + getToSection(req) + '"';
  el.style.position = 'absolute';
  el.style.cursor = 'pointer';
  return document.body.appendChild(el);
}

// this function acts as almost like a model for this example
// generally you'd either load your model from a server or
// have a static model object
function getToSection(req) {
  return {
    '/': '/about',
    '/about': '/contact',
    '/contact': '/'
  }[ req.route ];
}
```

Usually the first thing you'll do is setup a `bigwheel` framework instance:
```javascript
var framework = bigwheel( function(done) {
  return {
    routes: {
      '/': Section,
      '/about': Section,
      '/contact': Section
    }
  };
});
```
The function passed to `bigwheel` should either return a "settings" object which will be used to setup `bigwheel` the settings object can can also be returned via the callback which will be passed to this function. In this case this callback is called `done`. To pass back the settings object assynchronously you'd do it like this:
```javascript
var framework = bigwheel( function(done) {

  // do something assynchonous here and then call the
  // the done function returning the settings object
  done( {
    routes: {
      '/': Section,
      '/about': Section,
      '/contact': Section
    }
  });
});
```

In the above code the `bigwheel` is created and routes are defined. Routes are associated to "Sections". A section can be a page which handles:
1. initializing a view
2. resizing a view
3. animating in the view
4. animating out the view
5. destroying or unintializing the view

This is all created here:
```javascript
function Section() {

  var el;
  
  return {

    // the init function creates the view and initializes it
    // after init finishes the view should not be visible
    init: function(req, done) {
      el = createEl(req);      
      el.onclick = function() {
        framework.go(getToSection(req));
      };
      done();
    },

    // the resize function will be called imediately after init
    // here you can apply "responsive" calculations on your view
    resize: function(width, height) {
      var fontSize = width / 500 * 30;
      el.style.fontSize = fontSize + 'px';
      el.style.top = Math.round(( height - fontSize ) * 0.5) + 'px';
    },

    // in animateIn you'll animate in your hidden content that
    // was created in init
    animateIn: function(req, done) {
      Tween.from(el, 1, {
        y: -100, 
        opacity: 0,
        ease: Back.easeOut, 
        onComplete: done
      });
    },

    // in animateOut you'll animate out your content that
    // was created in init
    animateOut: function(req, done) {
      Tween.to(el, 0.25, {
        y: 100, 
        opacity: 0, 
        ease: Back.easeIn, 
        onComplete: done
      });
    },

    // in destroy you'll clean up the content which was
    // created in init
    destroy: function(req, done) {
      el.parentNode.removeChild(el);
    }
  };
}
```

Things to note here.

`init`, `animateIn`, `animateOut` receive two variables `req` and `done`. 

`req` contains routing information. In `init` and `animateIn` `req` contains the routing information that caused this section to be resolved. `animateOut` receives routing information which caused this section to be taken out.

You can change the route which `bigwheel` is on by calling:
```javascript
framework.go(getToSection(req));
```