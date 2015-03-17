# Defining Routes

This part of the documentation will focus solely on defining routes for `bigwheel`.

Routes are defined as an `Object` and it would look like this:
```javascript
module.exports = {
    '/': require('../sections/Landing'),
    '/about': require('../sections/About')
};
```

When defining Routes for `bigwheel` you'll always be defining url like `String`s which will be associated with a [section](sections.md).

Even though we're simply associating strings to sections there are three ways to do this:
1. [Using a section (standard form)](#as-section-standard-form)
2. [Using a section descriptor](#as-section-descriptor)
3. [Using multi sections](#multi-section-routes)


## As section (standard form)

Most of the time your routes will look like the above:
```javascript
'/about': require('../sections/About')
```
In this case if the user were to go to: `http://localhost:8080/#!/about` the section defined at `require('../sections/About')` would be run (`bigwheel` will go through `About`'s `init`, `resize`, `animateIn`, `animateOut`, `destroy` methods)

**_Note currently `bigwheel` uses "hash bang" routing but could easily be made to handle the history api_**

**_Gotcha: In all these examples we've been defining routes inline in objects.
It might be tempting to require sections outside of the routes object but this can cause a large gotcha for instance if we did:_**
```javascript
var Section = require('../sections/TheSection');

module.exports = {
    '/': Section
};
```
**_This may cause issues because of [circular dependencies](http://selfcontained.us/2012/05/08/node-js-circular-dependencies/). For instance `Section` may require in the instance of bigwheel and use the `go` method of `bigwheel` to change sections. So in this case:
- framework (instance of bigwheel) requires routes
- routes requires Section
- Section requires framework

If a circular dependency is created then `Section` requiring the framework would just receive an empty `Object`_**

## As section descriptor

Another way in which you can definea section is by using a section descriptor `Object`. Setting up routes via section descriptors could look like this:
```javascript
module.exports = {
    '/': require('../sections/Landing'),
    '/about': { section: require('../sections/About') },
    '/secret': { section: require('../sections/About'), useURL: false },
    '/gallery/:id': { section: require('../sections/About'), duplicate: true }
};
```

As you can see you can describe routes in the fashion you've already learned where a section is associated with a string but you can also define a route by passing an `Object` which may have extra options to tell `bigwheel` how to handle that route.

When defining sections from a section descriptor `Object` you'll always pass a variable called `section` which should be the required section `function` or `Object`.

After this there are two properties which can use to modify how routes are handled:

### `useURL`

`useURL` tells `bigwheel` that the Browsers location should be changed when the section is brought in. By default `bigwheel` will change the url when navigating to sections (`useURL == true`).

If you set `useURL` to `false` then the url will never bed updated and also the section cannot be accessed by manually updating the browser's location.

In the above routes example if a user went in and typed:
```javascript
http://localhost:8080/#!/secret
```

The secret section would not come up. 

The only way `/secret` can be accessed is by using `bigwheel`'s `go` function. So:
```javascript
framework.go('/secret');
```
Would bring up the `/secret` section.

### `duplicate`

Hypothetically with the routes:
```javascript
module.exports = {
    '/about': { section: require('../sections/About') },
    '/gallery/:id': { section: require('../sections/About'), duplicate: true }
};
```

If you had a button in the `/About` section that when clicked would do:
```javascript
framework.go('/about');
```

Nothing would happen. `bigwheel` ensures that a section cannot be brough up if it's the current section in view.

Now there are cases where you do want this functionality. For instance if you had a gallery which showed many gallery items using the same route. Like in the example above `/gallery/:id` would resolve to:
```
/gallery/10
/gallery/dog
/gallery/1b
```
This is handy because you can create one section that will bring up many different types of gallery items. The issue is that `bigwheel` would prevent any route that resolves to `/gallery/:id`.

To get around this you can do `duplicate: true` when defining routes using a section descriptor. Doing `duplicate: true` states that this route can be duplicated or opened multiple times.

## Multi section routes

Sometimes you may want to run multiple sections when a route is resolved. A common case for this is when using a menu. Setting your routes this way look like this:
```javascript
module.exports = {
    '/': [ require('../sections/Menu'), require('../sections/Landing') ]
    '/about': { section: [ 
        require('../sections/Menu'), 
        require('../sections/About') 
    ], useURL: false }
};
```

As you can see to define multi section routes you just define your sections in an array using the standard form or using a section descriptor. What will happen is that when the route resolves all the sections will be run in tandem so all `init`, `resize`, `animateIn`, `animateOut`, and `destroy` calls will happen at the same time for all multi sections.

The reason why this is handy is that if you define `Menu` as an `Object` instead of a `function` `Menu` will always be persistent and it can react to sections coming in for instance highlighting the correct menu button in the `animateIn` function.