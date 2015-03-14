# The `animateIn` and `animateOut` method's of a Section

You might remember each section may contain five methods: `init`, `resize`, `animateIn`, `animateOut`, `destroy`.

We will discuss both the `animateIn` and `animateOut` methods in detail here since they are quire similar.

## `animateIn`

`animateIn` has one sole purpose and that is to visually bring in content via animations. It is called immediately after the `init` method is finished.

The `animateIn` method assumes that content should be in an out state which was defined in the `init` section.

For instance a simple section might look like this:
```javascript
module.exports = Section;

function Section() {}

Section.prototype = {
    init: function(req, done) {
        this.el = document.createElement('div');
        this.el.innerHTML = 'Something something';
        this.el.style.opacity = 0;
        document.body.appendChild(this.el);

        done();
    },

    animateIn: function(req, done) {
        this.el.style.opacity = 1;

        done();
    }
};
```

In this example there really isn't an animation but it will simply show what should happen in the `animateIn` function. As you can see in the `init` method we create a `div` element, set it's content, add it to the body, and set it's `opacity` to `0`. Setting it's opacity to `0` ensures that it can't be seen until it's animated in. (obviously you could do this through an external style sheet). When the `animateIn` method is called by `bigwheel` the element's `opacity` is set to `1` making it visible and then `bigwheel` is notified that the animation for animate in is complete via the `done` parameter.

Of course you could use the excellent Tweening module `gsap` to perform animations:

```javascript
module.exports = Section;

var Tween = require('gsap');

function Section() {}

Section.prototype = {
    init: function(req, done) {
        this.el = document.createElement('div');
        this.el.innerHTML = 'Something something';
        this.el.style.opacity = 0;
        document.body.appendChild(this.el);

        done();
    },

    animateIn: function(req, done) {
        Tween.to(this.el, 0.5, { opacity: 1, onComplete: done });
    }
};
```

Now that's getting a bit fancier where the element is tweened from `opacity` `0` to `1` and when the Tween is finished the `done` parameter is called.

**_Gotcha: Remember to always call the `done` parameter otherwise `bigwheel` will not know the animation for `animateIn` is finished_**

The `req` parameter which is passed to `animateIn` is exactly the same as what is passed to the `init` method. It still maybe beneficial to create dynamic animations based on the routing information.

## `animateOut`

`animateOut` also has one sole purpose to animate out content. It functions exactly the same as `animateIn`. Our simple example would look this with an `animateOut` function:

```javascript
module.exports = Section;

function Section() {}

Section.prototype = {
    init: function(req, done) {
        this.el = document.createElement('div');
        this.el.innerHTML = 'Something something';
        this.el.style.opacity = 0;
        document.body.appendChild(this.el);

        done();
    },

    animateIn: function(req, done) {
        this.el.style.opacity = 1;

        done();
    },

    animateOut: function(req, done) {
        this.el.style.opacity = 0;

        done();
    }
};
```

**_Again remember to call done as `bigwheel` will not know that `animateOut` has finished if you do not_**

`animateOut` is called only if `bigwheel` is bringing in a new section and that new section has initialized.

One major difference between `animateIn` and `animateOut` is that the `req` parameter actually contains the routing information which was used to bring in the new content.

So in our case let's say our current section was brought in using the route `'/landing` and the url for the site has changed. The new route/url will be `'/about`. `animateOut`'s `req.route` will contain the value `/about` even though our section is `/landing`. This is done on purpose to allow your animations to be responsive to which section is coming in.

For instance you could have two routes `/left` and `/right`. When `animateOut` is called `req.route` could have the value `'/left'` in which case we could animate our element's `style.left` to `-100px`. However if the new section coming in would be `/right` `req.route` for `animateOut` would be `'/right'` in which case we could animate `style.left` to `100px`.

Here's what it would look like in code:
```javascript
module.exports = Section;

var Tween = require('gsap');

function Section() {}

Section.prototype = {
    init: function(req, done) {
        this.el = document.createElement('div');
        this.el.innerHTML = 'Something something';
        this.el.style.opacity = 0;
        document.body.appendChild(this.el);

        done();
    },

    animateIn: function(req, done) {
        Tween.to( this.el, 0.5, { opacity: 1, onComplete: done });
    },

    animateOut: function(req, done) {
        if(req.route == '/left') {
            Tween.to( this.el, 0.5, { 
                left: -100,
                opacity: 0, 
                onComplete: done 
            });    
        } else if(req.route == '/right') {
            Tween.to( this.el, 0.5, { 
                left: 100,
                opacity: 0, 
                onComplete: done 
            });
        } 
    }
};
```
