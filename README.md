# LambdaGrid Dashboard

Enterprise dashboard frontend infrastructure, to allow software engineers to build complex enterprise single-page app dashboards in just a simple, interactive REPL.

> Request beta access by emailing us at support@lambdagrid.com with subject line "LambdaGrid Dashboard beta access request"!

## What is the LambdaGrid Dashboard?

It's infrastructure which is open source, for single-page apps, and meant for enterprise dashboards. On top of this infrastructure of stable APIs, developers can build miniature applications to configure and customize their enterprise dashboards. These mini apps are just deterministic, stateless functions, which makes each mini app easy to build and test.

## Why does LambdaGrid Dashboard exist?

Frontend development is hard.

Coding a dashboard with LambdaGrid Dashboard should feel as simple and interactive as coding inside a REPL. But instead, coding a dashboard requires hair pulling. You have to configure React, Redux, and a bunch of other libraries, just to make a ugly, rudimentary dashboard that's not shippable to end users.

LambdaGrid Dashboard enables software engineers to build enterprise-grade dashboards just inside of an interactive REPL with stateless functions, which makes it easy for developers to build and test their mini apps.

## Who is LambdaGrid Dashboard for?

Any software engineer who's responsible for delivering an enterprise-grade dashboard to paying customers. LambdaGrid is especially great for engineers with beginner to intermediate frontend experience.

> Request beta access by emailing us at support@lambdagrid.com with subject line "LambdaGrid Dashboard beta access request"!

# Usage

## 5-minute initial tour of LambdaGrid Dashboard

```bash
$ mkdir dashboard && cd dashboard # make a new directory for your dashboard
$ cd dashboard
$ npm install lgas                # "lgas" stands for "LambdaGrid Application Services"
$ lgas dashboard init             # adds boilerplate LambdaGrid dashboard folder structure to current directory


$ tree -d -L 2 -I node_modules .  # `brew install tree`
.
├── black-box-tests
└── src
    ├── actions
    ├── constants
    ├── components
    ├── pages
    ├── reducers
    ├── requests
    └── routes


$ tree src/components             # let's peek into components, which are reusable React components
src/components
├── text-input
│   ├── text-input.js
│   ├── text-input.snapshot.js
│   └── text-input.spec.js
└── checkbox
    ├── __checkbox.snapshot0.png
    ├── __checkbox.snapshot1.png
    ├── checkbox.js
    ├── checkbox.snap
    └── checkbox.spec.js


$ cat src/components/checkbox/checkbox.js
import React from 'react';

export default function Checkbox(props) {
  return (<div class="form-check">
    <input class="form-check-input" type="checkbox" value={props.value} id={props.id} disabled={props.disabled} />
    <label class="form-check-label" for={props.id}>{props.label}</label>
  </div>);
}


$ cat src/components/checkbox/checkbox.spec.js
import { describe, it, expect } from 'lambdagrid';
import Checkbox from './checkbox';

describe('Checkbox', () => {
  it('should render the label', () => {
    const labelProps = { label: 'This is my checkbox label!' };
    expect(Checkbox(labelProps)).toMatchRenderedSnapshot();
  });

  it('should be disabled', () => {
    const disabledProps = { disabled: true };
    expect(Checkbox(disabledProps)).toMatchRenderedSnapshot();
  });
});


$ cat src/components/checkbox/checkbox.snap
exports[`Checkbox should render the label`] = render('__checkbox.snapshot0.png')
exports[`Checkbox should be disabled`] = render('__checkbox.snapshot1.png')
```

> Request beta access by emailing us at support@lambdagrid.com with subject line "LambdaGrid Dashboard beta access request"!

## Development with LambdaGrid

First, run the interactive development REPL:

```bash
$ lgas dashboard repl \
    --input    src/components/text-input/text-input.js \
    --spec     src/components/text-input/text-input.spec.js \
    --snapshot src/components/text-input/text-input.snap
Now running interactive development environment at https://dashboard-repl.lambdagrid.com/dbb804f87fdc6914a7978aae54f5958b
Rendered 5752cc38eb5d8ff63890bbc48742001d at 5:41pm on Tue Apr 17.
Watching your input and spec files for changes...
```

When you navigate to the URL in your browser, you'll see snapshot output, as if you're running local tests inside your terminal.

Then, update your spec and input files as you see fit. As you update your files, your online REPL environment will re-evaluate your files and print the output into the output section, which also allows you to update snapshots. This is the rapid feedback loop that LambdaGrid aims to provide.

## Testing

There are two types of testing that LambdaGrid Dashboard provides:
1. White box tests to ensure correctness of your mini apps
2. Black box tests for your entire application to ensure correct end-to-end behavior

### White box tests

White box tests verify the behavior of your mini apps. You write specifications in your `.spec.js` files, and then the LambdaGrid Dashboard REPL runs the specs to produce snapshots. When either the spec or input of your mini app changes, the snapshot changes too. If a changed snapshot was intentional, the LambdaGrid Dashboard REPL can update the snapshot.

Mini apps are just stateless functions, so specs are simple to design and snapshots are simple to understand. You can check your specs and snapshots into version control. White box tests go in the `/src` directory.

### Black box tests

Black box tests verify the behavior of your entire application from the end user's perspective. They are end-to-end tests, or integration tests.

Here's a quick tour of a black box test:

```bash
$ cd dashboard
$ tree -d -L 1 black-box-tests
black-box-tests
├── credit-card.js
├── new-user-onboarding.js
└── user-login.js

$ cat black-box-tests/credit-card.js
import { user, describe, it, expect } from 'lambdagrid';
import Dashboard from '../src/index.js';

describe('Adding first credit card', () => {
  const loggedIn = user.login(Dashboard);
  const creditCardsPage = user.navigate(Dashboard, '/credit-cards');

  it('shows no credit cards at first', () => {
    expect(creditCardsPage).toMatchRenderedSnapshot();
  });

  const newCreditCardPage = user.click(creditCardsPage, '#new-credit-card');

  it('shows new credit card form', () => {
    expect(newCreditCardPage).toMatchRenderedSnapshot();
  });

  const badCreditCardSubmission = user.chainActions(
    newCreditCardPage,
    user.focus('#credit-card-number'),
    user.change('4242424242424242'),
    user.submit('#submit-credit-card')
  );

  it('shows credit card form submission errors', () => {
    expect(badCreditCardSubmission).toMatchRenderedSnapshot();
  });

  const goodCreditCardSubmission = user.chainActions(
    badCreditCardPage,
    user.focus('#credit-card-number'),
    user.change('4242424242424242'),
    user.focus('#credit-card-cvc'),
    user.change('999'),
    user.submit('#submit-credit-card')
  );

  it('successfully submits a credit card', () => {
    expect(goodCreditCardSubmission).toMatchRenderedSnapshot();
  });
});
```

> Request beta access by emailing us at support@lambdagrid.com with subject line "LambdaGrid Dashboard beta access request"!

## Continuous Integration, Delivery & Deployment

LambdaGrid Dashboard enables continuous integration, delivery & deployment of your application. The more tests you write, the more value this feature can deliver for you.

To enable continuous integration:

```bash
$ cd dashboard
$ lgas dashboard ci --switch on
Continuous integration is enabled for your dashboard! To start using it, do the
following:
1. Pick branches on GitHub to trigger CI. Most people pick `master`, `staging`,
and `production`.
2. Create GitHub webhooks for each branch on the `push` event. If you'd like,
learn more about webhooks here: https://developer.github.com/webhooks/)
3. Have the webhooks send a POST request to
https://dashboard-ci.lambdagrid.com/8cda79fafb296e7b287b2117700c4e51 to trigger
our continuous integration
```

To enable continuous delivery:

```bash
$ cd dashboard
$ lgas dashboard cdel --switch on
Continuous delivery is enabled for your dashboard! To start using it, do the
following:
1. Pick branches on GitHub to trigger continuous delivery. Most people pick
`master`, `staging`, and `production`.
2. Create GitHub webhooks for each branch on the `push` event. If you'd like,
learn more about webhooks here: https://developer.github.com/webhooks/)
3. Have the webhooks send a POST request to
https://dashboard-ci.lambdagrid.com/c8da79fafb296e7b287b2117700c4e15 to trigger
our continuous delivery
```

Enabling continuous deployment is similar:

```bash
 cd dashboard
$ lgas dashboard cdep --switch on
Continuous deployment is enabled for your dashboard! To start using it, do the
following:
1. Pick branches on GitHub to trigger continuous deployment. Most people pick
`master`, `staging`, and `production`.
2. Create GitHub webhooks for each branch on the `push` event. If you'd like,
learn more about webhooks here: https://developer.github.com/webhooks/)
3. Have the webhooks send a POST request to
https://dashboard-ci.lambdagrid.com/c8da79fafb296e7b287b2117700c4e15 to trigger
our continuous deployment
```

## Monitoring

If you deployed your enterprise dashboard application with LambdaGrid, you also have access to LambdaGrid Dashboard monitoring. You'll get access to the following benefits:
- Be confident your code works as designed with runtime exception tracking
- Know your application is interacting with APIs over the network correctly with network request monitoring

> Request beta access by emailing us at support@lambdagrid.com with subject line "LambdaGrid Dashboard beta access request"!
