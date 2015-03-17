# Routes

Routes are url partials which are associated to [Sections](sections.md).

What that basically means is that you will define strings/url's which will execute [Sections](sections.md).

For example you could define your routes like this:
```javascript
module.exports = {
    '/': require('../SectionLanding'),
    '/about': require('../SectionAbout')
};
```

Routing is handled by the module [bw-router](https://www.npmjs.com/package/bw-router) which primarily uses the module [routes](https://www.npmjs.com/package/routes).

`bw-router` primarily watches the `window.location.hash` for changes. Once a change occurs it will try to figure out which [Sections](sections.md) should be opened.

For instance our Browsers url might look something like:
```
http://localhost:8080/#!/about
```
In this case the route `'/about': require('../SectionAbout')` would be used and `SectionAbout` would initialized and then animated in.

Something to note is that `bw-router` can work outside of the browser. For instance `bigwheel` can be used as a framework to create command line applications, or be used in browser like environments like [CocoonJS](https://www.ludei.com/cocoonjs/). There is no relliance on `window.location`. In this case simply `bigwheel`'s `go` method would be used to cause the router to change sections. (more on this later)