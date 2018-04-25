# LambdaGrid Dashboard

Build a professional customer-facing login web dashboard with just deterministic functions.

In more detail, LambdaGrid Dashboard is open source infrastructure that allows software engineers to build an enterprise dashboard single-page application with speed and correctness. It's a new opinionated framework to allow professional frontend engineers to not only do their best work, but also have it be appreciated by the rest of the team.

# What makes LambdaGrid different for software engineers?

## Emphasis on enabling a state of flow

The first major design principle that makes LambdaGrid different is the intent to enable flow for all developers.

Software engineers are most productive when in a state of flow. You can create a LambdaGrid dashboard quickly because of the following design principles to enable flow for all software engineers:

### Rapid feedback loops

LambdaGrid aims to enable states of flow with a rapid feedback loop. We provide a command line tool with familiar REPL behavior:
1. We watch for changes to your source files and your test files
2. We evaluate your source files (the deterministic functions) as specified in your test files (providing inputs for the deterministic functions)
3. We print the output of the deterministic functions

Engineers can be in flow and close many feedback loops per minute with a LambdaGrid REPL.

### Deterministic functions on open source infrastructure

All source code inside a LambdaGrid Dashboard application is deterministic functions. Engineers can create deterministic functions that run with LambdaGrid infrastructure to configure and customize their entire dashboard.

### Library of Bootstrap-compatible React components

Many engineers want to write as little HTML and CSS as possible. To that effect, LambdaGrid has open source React components that are available for free to all users. The components are all Bootstrap-compatible, so you can upload a custom Bootstrap theme to make your application look exactly the way you'd like while leveraging Bootstrap's cross-browser compatibility and performance benefits.

## Emphasis on trustworthiness

The second major design principle that makes LambdaGrid different is the intent to be trustworthy.

LambdaGrid Dashboard is designed to produce applications that earn their developers' trust. Software engineers trust their applications when they are reliable, communicative, and inspectable. We want engineers to have as few "WTF??" moments and "ARGH!!" moments as possible. LambdaGrid was built with the following design principles in mind:

### Tests that encourage other engineers to write more tests

LambdaGrid provides testing solutions, both at a lower level for all deterministic functions at at a higher level to create end-to-end tests, requiring zero configuration or installations. We designed LambdaGrid testing solutions to be so delightful that all your coworkers will write tests, leading to your dashboard application's code base being healthier and more stable.

We want engineers to be excited to write more tests, and even more excited about what LambdaGrid does to encourage their coworkers to write tests.

### Continuous integration, delivery & deployment

LambdaGrid can manage all continuous integration, delivery & deployment for your team. This includes code changes in not only your own source code of deterministic functions, but also your tests. LambdaGrid manages all the complexities and headaches of a continuous integration, delivery & deployment pipeline so engineers can focus on staying in flow building applications.

### Managed updates to the LambdaGrid infrastructure

Not everything is going to work all the time. When LambdaGrid patches bugs or delivers new features to the Dashboard infrastructure, we take care of the managed updates for you. If a migration is required, we document the migration process so that the migration is as smooth as possible.

## Emphasis on easy switching to (and from) LambdaGrid Dashboard

The third major design principle that makes LambdaGrid different is the migration story.

Most engineers already have a dashboard code base, and not every engineer is ready to throw out an existing code base just to start using LambdaGrid Dashboard. Conversely, not every engineer is ready to adopt a new developer tool for fear of vendor lock-in.

### How to switch to LambdaGrid Dashboard

You can build 80% of your dashboard in one week with LambdaGrid, whether you're building a completely new dashboard or migrating an existing dashboard from an old o code base. We recommend new users to build their dashboards first with declaring the application state structure, then by declaring the routing strategy, then by creating pages for each route, and finally by adding user interactions. See more [here](#getting-started) to see how to get started.

### How to switch from LambdaGrid Dashboard

Since LambdaGrid Dashboard's infrastructure is all open source, you can switch off from LambdaGrid at any given time and still access LambdaGrid's core technology for free by forking the LambdaGrid Dashboard repository on GitHub. Although you'll lose access to the closed LambdaGrid features that make the developer experience more usable, you can still be a free user of LambdaGrid's infrastructure and still create applications with just deterministic functions.

# Getting started

LambdaGrid Dashboard applications are comprised of just deterministic functions. With this design around deterministic functions, we intend to allow users to create their dashboards very quickly, with 80% of the initial work done within the first week. Into your source control, you can check in not just these deterministic functions, but also their tests and 3rd party dependencies like Lodash and D3.

```bash
$ cd dashboard
$ tree -d -L 1 ./src
./src
├── actions
├── pages
├── routes
├── reducers
├── ui-components
└── utils
```

## Example: Todo app dashboard

Let's build the customary todo list application with LambdaGrid Dashboard to demonstrate its functionality.

### First, declare router

The `routes` folder contains a single deterministic function to declare your URL routing strategy in `index.js`, with test files in `index.spec.js` and `index.snap`.

```bash
$ tree ./src/routes
├── index.js
├── index.spec.js
└── index.snap
```

The index file in the `routes` folder contains just a function whose inputs are the requested URL and the current application state, and whose output is the expected routing result.

```javascript
// src/routes/index.js
import Todos from '../pages/Todos';
import Login from '../pages/Login';
import Friendly404 from '../pages/404';
import { isLoggedIn } from '../utils';

export default function router(requestedUrl, state) {
  const isLoggedIn = isLoggedIn(state.user);

  if (isLoggedIn) {
    // private routes go here
    if (requestedUrl.path === '/') {
      return { status: 200, page: Todos };
    } else if (requestedUrl.path === '/login') {
      return {
        status: 301,
        location: '/',
        flash: { type: 'info', msg: 'Already logged in.' }
      };
    }
    return { status: 404, page: Friendly404 };

  } else {
    // public routes go here
    if (requestedUrl.path === '/login') {
      return { status: 200, page: Login }
    }

    return {
      status: 301,
      location: '/login',
      flash: { type: 'danger', msg: 'Requires login.' }
    };
  }
}
```

### Next, create a page

A `page` comprises of a) four different deterministic functions and b) a UI component.

```javascript
// src/pages/Todos.js
import Todos from '../components/Todos';
import { toggleTodo, receivedTodos } from '../actions';

export function mapStateToProps(state) {
  function filterTodos() {
    switch (state.filter) {
      case 'SHOW_COMPLETE':
        return state.todos.filter(t => t.complete);
      case 'SHOW_INCOMPLETE':
        return state.todos.filter(t => !t.complete);
      case 'SHOW_ALL':
      default:
        return state.todos;
    }
  }

  return { todos: filterTodos() }
}

export function mapDispatchToProps(dispatch) {
  return {
    onTodoClick: id => dispatch(toggleTodo(id))
  };
}

export function onIncompleteData(http, done) {
  return http.get('https://api.todo.com/todos')
    .then(response => done(receivedTodos(response)));
}

export const Component = Todos;
```

The first and second deterministic functions are `mapStateToProps` and `mapDispatchToProps` as in Redux. The former selects properties from the application state to pass into the UI components. The latter creates event handlers to capture user events like clicks, keystrokes, and form submissions.

The third deterministic function is an HTTP API request dispatcher which triggers when `mapStateToProps` can't get all the data needed from state, so LambdaGrid Dashboard will fetch the data needed.

Finally, the single UI component that's exported is the React component that will receive the outputs of `mapStateToProps` and `mapDispatchToProps`.

### Finally, create your UI components and Redux actions & reducers

Your UI components and Redux actions & reducers are also all just deterministic functions. The deterministic functions that are your UI components take their props as an input and return a React component. The functions that are Redux actions take an input that you as the user structure inside `mapDispatchToProps` and return an action object for Redux reducers to process. The functions that are Redux reducers take the current application state and the pending Redux action object and return the new application state.

# FAQ

## What's the rationale behind LambdaGrid Dashboard?

Most developers find professional dashboards cumbersome to build. Most engineering managers find professional dashboards expensive to build. LambdaGrid Dashboard is an attempt at solving both problems.

## How does LambdaGrid expect to make money out of the Dashboard product?

LambdaGrid Dashboard's core infrastructure is open source, so we don't expect to make money off of our entire userbase. Beyond the infrastructure to provide the functionality, features that make LambdaGrid Dashboard more reliable and usable are closed source.

LambdaGrid Dashboard users can use LambdaGrid Dashboard features for free for all public applications, or become paying users for private applications.
