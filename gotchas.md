# Gotchas

Here we'll discuss some typical gotchas that you might encounter when using `bigwheel`.

## Forgetting To Call Done

So you might remember from the documentation for the [`init` function for Sections](sections-init.md) one of the biggest gotchas is not calling the done callback after the `init`, `animateIn`, `animateOut` functions have completed.

If you do this you're application will have unexpected results such as the `init` section hanging if `done` is not called. Or if you do not call the `done` callback from `animateOut` your section may never be destroyed.

## Circular Dependendencies

In most module system it's possible to create circular dependencies. What this means is that:

1. A requires 
2. B requires A

This would cause a circular dependency. When this happens with CommonJS modules a "blan" Object is returned to B when requiring A.

The most common place this happens in bigwheel is when you're defining routes.

Often a section will require the framework instance in order to be able to use the `go` method of `bigwheel`. To get around this you should always require your sections inline when defining routes:
```javascript
// good
var bigwheel = require('bigwheel');

var goodFramework = bigwheel( function() {

    return {
        routes: {
            '/': require('../sections/Landing'),
            '/about': require('../sections/About')
        }
    };
});


// bad
var bigwheel = require('bigwheel');
var Landing = require('../sections/Landing');
var About = require('../sections/About');

var goodFramework = bigwheel( function() {

    return {
        routes: {
            '/': Landing,
            '/about': About
        }
    };
});
```
