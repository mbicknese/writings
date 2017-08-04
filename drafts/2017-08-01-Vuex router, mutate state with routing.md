---
title: Vuex router, mutate state with routing
date: 01-08-2017
tags: concept, tech
---

Back in my AngularJS days, we used [ui-router][1]. One of the interesting aspects of the router was the fact that it encouraged using the routes as state manipulators. Meaning, instead of saying route X maps to page Y. One would define route X sets the state to this predefined set. Which in turn might correspond to displaying page Y. Since then I forgot about this way of working while building applications in [Vue.js][2] (Vue).

Lately I've been toying with the idea of bringing state mutations through routing back to Vue. It could offer some great benefits:

 * Reactive UI displaying the transition from one state to the other. As opposed to just bluntly remove the component representing page Y and replacing it with the component displaying page Z.
 * Living application driven by state and state mutations.
 * Routing on any nested level within the component hierarchy.
 * Potentially it could even speed up development as hot-swapping components or live reloading the page can retain the same state.
 * Auto login, and other actions, through parameterized URLs.

And if I keep brainstorming I could possibly end up with more benefits. The point is, I'm excited about it! All what is left to do is to actually build this [Vuex][3] router and put it to the test.


[1]: TODO Link to ui-router
[2]: TODO Link to Vue.js
[3]: TODO Link to Vuex
