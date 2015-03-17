# The `init` method of a Section

As you may know now each section may contain five methods: `init`, `resize`, `animateIn`, `animateOut`, `destroy`.

We will now discuss the `init` method in detail.

## `init`

The purpose of the `init` method is to initialize your content. In many cases it maybe adding new dom elements to the dom, or if working with WebGL initializing buffers, loading models etc.

Lets create an `init` method which just brings in a new `HTMLImageElement`.

```javascript
module.exports = MySection;

function MySection() {}

MySection.prototype = {
    init: function(req, done) {
        this.image = new Image();
        this.image.src = './someImage.jpg';
        document.body.appendChild(this.image);

        done();
    }
};
```

Now our section on `init` would create and add a new image to the `<body>`.

Our example is nice but there's a couple of issues. As stated before `bigwheel` works by bringing in and out sections by going through `init`, `animateIn`, and `animateOut` methods. 

### Issue #1: two pages on screen at the same time

The `init` method for the new section is called immediately after `bigwheel` receives a section change notification. This means the current section will be on screen when the new sections `init` method is called. Currently in our example the old content will be on screen and this new content (our image from `'./someImage.jpg'`) will be on screen at the same time.

To resolve this we could for instance set the css `display` property to `none`.

```javascript
module.exports = MySection;

function MySection() {}

MySection.prototype = {
    init: function(req, done) {
        this.image = new Image();
        this.image.src = './someImage.jpg';
        this.image.style.display = 'none';
        document.body.appendChild(this.image);

        done();
    }
};
```

Now what would happen is that the old content which maybe there will still remain on screen and this new content (our image) comes in but is invisible. In the `animateIn` method we'll then make it visible again.

### Issue #2: notifying `bigwheel` we're done before we actually are

Another issue we have is that technically we're notifying `bigwheel` that we're finished initializing when in reality it would be nicer to do that once our image has finished loading.

Our code could look like this:

```javascript
module.exports = MySection;

function MySection() {}

MySection.prototype = {
    init: function(req, done) {
        this.image = new Image();
        this.image.src = './someImage.jpg';
        this.image.style.display = 'none';
        document.body.appendChild(this.image);

        image.onload = done;
    }
};
```

The `done` parameter passed to our `init` method is a function which should be called once initialization is finished. So in our case we'd like to add the image to `<body>` and then wait for it to be loaded and then notify `bigwheel` that we're finished initializing our content. So we can just simply add the `done` paramter to the `onload` event handler of the `HTMLImageElement`.

**_It should be noted here that a huge gotcha is to forget to call the `done` parameter. ALWAYS remember to call the `done` parameter otherwise `bigwheel` will not know that your `init` method is finished and your application will sit there doing nothing._**

### `req`

You might be wondering what is the `req` parameter. `req` is an abreviation for request. It is an `Object` which contains information which was used to request `bigwheel` to bring in it's content. `bigwheel` works by using routes. Routes are essentially url's which open/show content. We'll discuss this further later but the `req` parameter allows your sections content to be dynamic.

The `req` parameter has one very key var variable called `route`. This was the route which was used to request this section.

For instance this section could be an about section it's route could look something like `'/about`. So when this section would come in `req.route`'s value would be `'/about'`. Let's look at how we could use this new knowledge.

```javascript
module.exports = MySection;

function MySection() {}

MySection.prototype = {
    init: function(req, done) {
        var sectionRoute = req.route;

        this.image = new Image();
        this.image.src = sectionRoute + '/someImage.jpg';
        this.image.style.display = 'none';
        document.body.appendChild(this.image);

        image.onload = done;
    }
};
```

We've assigned `req.route` to `sectionRoute`. So now in the above example if `sectionRoute` is `'/about'`. Our image would attempt to load from the following url `'/about/someImage.jpg'`. Now there's no reason why we could use this same section for multiple routes. For instance the `'/landing'` route could also bring in this section in which case the image would load from `'/landing/someImage.jpg'`.

I hope you can see how the `req` parameter can be used to make your content be dynamic.

### `bigwheel` and models

A more advanced case for using `req` would be to access a model. This could be either loading JSON content from a server or querying a static `Object`.

For instance we could have a static model on our site that looks like this:

```javascript
module.exports = {
    "/landing": {
        "title": "This is the title for Landing"
    },

    "/about": {
        "title": "This is the title for About"
    }
};
```

Our section for both routes `'/landing'` and `'/about'` could look like this:

```javascript
module.exports = Section;

// note we're requiring in the model defined above here
var model = require('../model');

function Section() {}

Section.prototype = {
    init: function(req, done) {
        var myModel = model[ req.route ];
        this.el = document.createElement('h1');

        this.el.innerHTML = myModel.title;
        document.body.appendChild(this.el);

        done();
    },

    destroy: function(req, done) {
        // remove the element
        this.el.parentNode.removeChild(this.el);
    }
};
```

In this case `Section` took `req.route` and queried the static model and took the contents of `.title` and set the `innerHTML` of our element. So if this section was opened using the route `/landing` our `h1` element's `innerHTML` would be `"This is the title for Landing"`. If the route to open this section was `/about` then the value of `innerHTML` would be `"This is the title for About"`