# accessible_spa_examples
Various single-page example apps that implement an accessible solution to improve view change awareness.

This repo provides working examples of single-page applications (SPA's) with improved accessibility.

## The Accessibility Problem with SPA's
 
SPA's seek to improve overall performance and efficiency by dynamically loading only the content and UI that needs to change vs. requesting and reloading many of the same assets with each page load.

One basic issue concerning SPA accessibilty is that assistive technology (screen readers in this case) are not aware of dynamically loaded content. Screen readers are tuned to document refreshes. It is up to us as UX designers and developers to ensure that pertinent, dynamic content or UI changes are brought to the attention of users.

In particular, when navigating from one view to another, no indication is provided to the user that anything has changed. This is unlike traditional page navigation via page refresh, where the screen reader provides a consistent pattern of alerting the user to the page refresh.


## An Accessible Solution

One solution is to emulate a native page refresh by announcing when a view/page loaded.

Generally, a page refresh is announced to the user by an aubible "loading" cue, the new page title, and often a spoken loading progress indicator.

We want to convey to the user:
- the new page/view title
- the view loaded status

## Implementation

Using a global "announcer" live region and a simple script, we can easily assemble a message and updating the announcer with the message. Screen readers in turn will pick up and announce this message.

<script src="http://gist.github.com/patrickfox/ed600b3b38c2cfab2b11.js"></script>

## Test Frameworks

The following framework examples are covered:

- React (0.14) - based on <a href="https://github.com/reactjs/react-router-tutorial" target="gh">react's react-router-tutorial</a>
- Angular 2 - based on the <a href="https://github.com/mgechev/angular2-seed" target="gh">angular2-seed project</a>
- Ember (2.4) - based on <a href="https://github.com/zoltan-nz/library-app" target="gh">zoltan-nz's library-app project</a>

Each framework folder contains a Readme.md with instructions for installing and running each project.


### Overview

Each of these frameworks provides lifecycle event hooks that allow us to determine when the view update is complete.

The goal is to call our `announce_view_loaded()` helper method after the view is rendered in the page.

### React

React provides a `componentDidUpdate` method that is fired after the initial component load. Each component.

In this example, all components/views are rendered by a parent component, `modules/App.js`. Whenever any update is made to the parent or child component, `componentDidUpdate()` will be invoked.

### Angular 2

Angular 2 provides a `ngAfterViewInit` method that is invoked every time a Component view is rendered. 

In this example, the 2 components (`home` and `about`) have their own folder that contains all component dependencies. Both `src/about/about.component.ts` and `src/home/home.component.ts` utilize `ngAfterViewInit` to invoke the `announce_view_loaded` helper method.

### Ember 2

In Ember 2, Ember 1.x views were scrapped in favor of using more generic components. Ember has the concept of a Run Loop with a sequence of queues that we can subscribe to. 

This Ember app uses a router file (`app/router.js`) to define and map each route. Route-specific actions are defined in a route file named for that particular route (e.g. `app/routes/about.js`). While Ember will automatically render a file with the same name as its route, Ember.Route provides an explicit `renderTemplate()` method in which we can specify the template file and subscribe to the `afterRender` event queue in the Ember Run Loop.

```
//from routes/about.js

export default Ember.Route.extend({
...
  renderTemplate(args) {
    this._super(...args);
    Ember.run.scheduleOnce('afterRender', this, function() {
      announce_view_loaded();
    });
  },
...
```

