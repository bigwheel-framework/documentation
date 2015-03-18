# Sections

What are sections? Sections are pages or parts of your application.

Sections perform the following: 

1. initialize content to be brought on screen
2. resize or make content be responsive
3. animate in that content
4. animate out that content when needed
5. destroying or removing that content off screen.

## Defining a section

An empty section would look like this. We've added comments to describe what happens in each method. We'll discuss each method in depth later:
```javascript
module.exports = MySection;

function MySection() {}

MySection.prototype = {
    init: function(req, done) {
      // In init you will:
      // 
      // 1. query the model
      // 2. create the page's view from the model. 
      //    eg. add dom elements to the dom
      // 3. ensure that view is in it's out state

      done();
    },

    resize: function(width, height) {
      // In resize you will:
      // 
      // 1. Resize content based on width and height passed in
    },

    animateIn: function(req, done) {
      // In animateIn:
      // 
      // 1. animate in your content which was defined in init. 
      //    eg. change the opacity of your dom elements to 1

      done();
    },

    animateOut: function(req, done) {
      // In animateOut:
      // 
      // 1. animate out your content which was defined in init. 
      //    eg. change the opacity of your dom elements to 0

      done();
    },

    destroy: function(req, done) {
      // In destroy:
      // 
      // 1. destroy the content which was created in init. 
      //    eg. remove dom elements from the dom

      done();
    }
};
```

Sections can be defined as prototypical classes using `function`s or by passing an `Object` instead. We'll talk about that later.

As a note if your application does not require animating in and out content you can simply ommit the `animateIn` and `animateOut` functions. `MySection` would look like this:

```javascript
module.exports = MySection;

function MySection() {}

MySection.prototype = {
    init: function(req, done) {
      // In init you will:
      // 
      // 1. query the model
      // 2. create the page's view from the model. 
      //    eg. add dom elements to the dom
      // 3. ensure that view is in it's out state

      done();
    },

    destroy: function(req, done) {
      // In destroy:
      // 
      // 1. destroy the content which was created in init. 
      //    eg. remove dom elements from the dom

      done();
    }
};
```

The section now would handle just initialize the content and then destroying that content. In fact all methods are optional for instance you could ommit the `init` and `destroy` methods and simply bring in your content in `animateIn` and take out your content in the `animateOut` method. This would be bad practice though you should separate animation from creation and destruction.

## How section methods are called

In `bigwheel` there is a view manager. It's purpose is to bring in and out sections. 

The view manager will always call the sections methods in the following order:

1. `init`
2. `resize`
3. `animateIn`
4. `animateOut`
5. `destroy`

The view manager calls the methods during specific point in time.

`init` is called when this section should be brought in.

`resize` is called initially right after `init` to initialize the size of the content in the section and later if the user resizes their Browser for instance.

`animateIn` is called after the initial `resize`.

`animateOut` is only called once the section has animated in and a new section is coming in. What would happen is that the new section's `init` would be called and when it's finished the current sections `animateOut` would be called.

`destroy` is called after animate out.
