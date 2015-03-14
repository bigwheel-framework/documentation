# The `animateIn` and `animateOut` method's of a Section

You might remember each section may contain five methods: `init`, `resize`, `animateIn`, `animateOut`, `destroy`.

Here we'll talk about `destroy`.

## `destroy`

Clearly `destroy` is the best named function in `bigwheel` but aside from that the purpose of `destroy` is to clean up any resources that were created for the section. In many cases it might be removing a `HTMLElement` from the dom.

For example:
```javascript
module.exports = Section;

function Section() {}

Section.prototype = {
    init: function(req, done) {
        this.el = document.createElement('h1');
        this.el.innerHTML = 'I\'m a title';
        document.body.appendChild(this.el);

        done();
    },

    destroy: function(req, done) {
        // remove the element
        this.el.parentNode.removeChild(this.el);
    }
};
```

Note that in destroy calling the `done` method is optional. Currently it does nothing but is there to keep the API consistent and allow for future uses.

The `req` parameter contains routing information for the new route/section that is coming in. You can think of it as the routing information causing this section to be destroyed.