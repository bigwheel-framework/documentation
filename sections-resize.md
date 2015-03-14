# The `resize` method of a Section

The five methods of a section are: `init`, `resize`, `animateIn`, `animateOut`, `destroy`.

We will now discuss the `resize` method in detail.

## `resize`

Responsive design for websites is practically a must today with the varied amount of resolutions for devices.

We believe responsive CSS is nice but there are still places to allow for responsive design through Javascript. For instance your website might be entirely built ontop of the `<canvas>` element or `<svg>` in which case all the fancy pants responsive CSS will do you no good.

This is where the `resize` method comes in.

An example of using `resize`:
```javascript
module.exports = Section;

function Section() {}

Section.prototype = {
    init: function(req, done) {
        this.el = document.createElement('div');
        this.el.style.position = 'absolute';
        this.el.style.background = '#F0F';
        document.body.appendChild(this.el);

        done();
    },

    resize: function(width, height) {
        var elWidth = width * 0.5;
        var elHeight = height * 0.5;

        this.el.style.left = ( width - elWidth ) * 0.5 + 'px';
        this.el.style.top = ( height - elHeight ) * 0.5 + 'px';
        this.el.style.width = elWidth + 'px';
        this.el.style.height = elHeight + 'px';
    }
};
```

In the above code we're creating a `<div>` element and setting it's background to be pink and setting positioning to be `absolute` and then letting `bighweel` know our initialization is finished.

Immediately after initialization is finished the `resize` function is called. We state wthat we want our pink element to be 50% of the page and we center it in the path. It looks a bit mathy but it's not bad when you start to look at it.

The `width` in the resize method in most cases will be equivalent to `window.innerWidth` and `height` will be `window.innerHeight` although this functionality can be overridden. (we'll talk about that later)