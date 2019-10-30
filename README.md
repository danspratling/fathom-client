# Fathom Client [![CircleCI](https://circleci.com/gh/unstacked/fathom-client.svg?style=svg)](https://circleci.com/gh/unstacked/fathom-client)

A [Fathom Analytics](https://usefathom.com/) library for environments with client-side routing.

Extracted from the [StaticKit](https://statickit.com) website.

## Installation

Run the following to install in your project:

```
npm i fathom-client
```

## Motivation

The standard installation flow for Fathom is to drop their snippet on your page, which will automatically load the library and track a pageview. This approach works great for server-rendered sites with full page refreshes, but gets tricky when:

- Routing happens on the client-side (e.g. an SPA)
- The DOM is abstracted away (e.g. Next.js)

This library provides an interface you can use to orchestrate Fathom calls at various points in your page lifecycle:

```js
import Fathom from 'fathom-client';

// Upon initial page load...
Fathom.load();
Fathom.setSiteId('XXXXXXXX');
Fathom.trackPageview();

// In the route changed event handler...
const onRouteChangeComplete = () => {
  Fathom.trackPageview();
};

// In an event handler where a goal is achieved...
const onSignUp = () => {
  Fathom.trackGoal('Sign Up', 100);
};
```

## Usage

### Next.js

Create an `_app.js` file in your `pages` directory, [like this](https://nextjs.org/docs#custom-app):

```js
import React from 'react';
import App from 'next/app';

class MyApp extends App {
  render() {
    const { Component, pageProps } = this.props;
    return <Component {...pageProps} />;
  }
}

export default MyApp;
```

Then, add a wrapper component with an effect to load Fathom on page load:

```diff
- import React from 'react'
+ import React, { useEffect } from 'react'
+ import Fathom from 'fathom-client'
  import App from 'next/app'

+ function Layout(props) {
+   useEffect(() => {
+     if (process.env.NODE_ENV === 'production') {
+       Fathom.load();
+       Fathom.setSiteId('<your-site-id>');
+       Fathom.trackPageview();
+     }
+   }, []);
+
+   return <div {...props} />;
+ }

  class MyApp extends App {
    render() {
      const { Component, pageProps } = this.props
-     return <Component {...pageProps} />
+     return (
+       <Layout>
+         <Component {...pageProps}></Component>
+       </Layout>
+     )
    }
  }

  export default MyApp
```

Finally, track a pageview any time the route changes:

```diff
  import React, { useEffect } from 'react'
  import Fathom from 'fathom-client'
  import App from 'next/app'
+ import Router from 'next/router';

+ Router.events.on('routeChangeComplete', () => {
+   Fathom.trackPageview();
+ });

  function Layout(props) {
    useEffect(() => {
      if (process.env.NODE_ENV === 'production') {
        Fathom.load();
        Fathom.setSiteId('<your-site-id>');
        Fathom.trackPageview();
      }
    }, []);

    return <div {...props} />;
  }

  class MyApp extends App {
    render() {
      const { Component, pageProps } = this.props
      return (
        <Layout>
          <Component {...pageProps}></Component>
        </Layout>
      )
    }
  }

  export default MyApp
```

## Releasing

Run the following to publish a new version:

```bash
npm run release
```
