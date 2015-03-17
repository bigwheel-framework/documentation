# Special Routes

In this page we'll discuss some of the special routes defined in `bigwheel`.

## Redirects

It's sometimes handy to be able to create a redirect. A redirect would look like this:
```javascript
module.exports = {
  '/': require('../sections/LandingSection'),
  '/landing': '/'
};
```

In this case both `'/'` and `'/landing'` would bring up the `LandingSection`.

## 404

Another handy functionality is to create a catch all 404 page. 404's can be defined this way:
```javascript
module.exports = {
  '/': require('../sections/LandingSection'),
  '404': require('../sections/FourOFour')
};
```

## initSection

`initSection` is not actually a part of defining routes however it's related to defining routes so this is an appropriate place to discuss `initSection`.

When you setup `bigwheel` you can do something like this:
```javascript
var bigwheel = require('bigwheel');

module.exports = bigwheel( function(done) {

  done( {
    initSection: require('../sections/Preloader'),
    routes: require('./routes')
  });
});
```

Routes are still defined as usual but alongside routes we've another property `initSection`. Basically the purpose of `initSection` is to have a section which is ALWAYS run before any routes are resolved. For instance if in `require('./routes')` we had defined `/about`. Even if the user went to:
```
http://someurl.com/#!/about
```
The `initSection` will be run before any of the sections defined by routes. This can be useful for things like preloaders or age of majority pages or some other page which "blocks" the user before they enter the site/application.

The actual section is defined exactly the same except it's constructor takes a callback function which should be called once the `initSection` is prepared to exit. So for instance if we were to write a preloader it would look something like this:
```javascript
module.exports = Preloader;

var somethingThatWillPreloadEverything = require('./awesomePreloader');

function Preloader(done) {
  this.onPreloadComplete = done;
};

Preloader.prototype = {
  init: function(req, done) {
    
    var el = this.el = document.createElement('div');
    el.style.background = '#CAFE00';
    el.style.height = '20px';
    el.style.display = 'none';
    document.body.appendChild(el);

    done();
  },

  animateIn: function(req, done) {
    var el = this.el;
    var onPreloadComplete = this.onPreloadComplete;
    var preloader = somethingThatWillPreloadEverything([
      './images/someImage1.jpg',
      './images/someImage2.jpg'
    ]);

    // watch for progress of the preloader
    preloader.onProgress = function(progress) {
      // show the progress of the load using a bar
      el.style.width = progress * 200 + 'px';

      // if the preloader is finished its going to be 1 or 100%
      if(progress == 1) {
        onPreloadComplete();
      }
    };

    // this will animateIn the el
    el.style.display = 'block';
    done();
  },

  animateOut: function(req, done) {

    // this will animateOut the el
    this.el.style.display = 'none';
  },

  destroy: function(req, done) {

    // remove the element from dom
    this.el.parentNode.removeChild(this.el);
  }
};
```