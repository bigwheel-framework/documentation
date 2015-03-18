# Miscellaneous

In this part of the documentation we want to cover some random things that might be overlooked with `bigwheel`.

## Auto Resize

If you are running `bigwheel` in the browser (`bigwheel` will work in other environments also). It will automatically add a listener to listen for `window` resize events. If you don't want this to happen you could pass a settings object to `bigwheel` that would look like this.
```javascript
module.exports = bigwheel( function() {
    return {
        autoResize: false,
        routes: require('../routes')
    };
});
```

If you pass false for `autoResize` then a listener won't be added to `window`. You can still propagate resizes to sections by calling `bigwheel`'s `resize` function like this:
```javascript
var bigwheelInstance = require('./framework/');

bigwheelInstance.resize(980, 570);
```

## Post Hash

You may have noticed while reading through this documentation that `bigwheel` uses "hash bang" urls. They look something like this:
```
http://someurl.com/#!/someRoute
```

If you'd like to have something else than hash bang you could do this:
```javascript
module.exports = bigwheel( function() {
    return {
        postHash: '#somethingElse',
        routes: require('../routes')
    };
});
```

If you did this all urls in would look like this:
```
http://someurl.com/#somethingElse/someRoute
```

## Overlap

If you've been running `bigwheel` examples you might've noticed that sections animate in and out functions are called at the same time. This means by default sections "overlap".

If you'd prefer to have sections stagger one section animates out and the next animates in after the animate out. You could pass `overlap: false` through settings:

```javascript
module.exports = bigwheel( function() {
    return {
        overlap: false,
        routes: require('../routes')
    };
});
```
