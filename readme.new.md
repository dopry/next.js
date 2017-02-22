# <img width="112" alt="NEXT.js" src="https://cloud.githubusercontent.com/assets/13041/19686250/971bf7f8-9ac0-11e6-975c-188defd82df1.png">

> A complete framework for Universal JavaScript applications with zero setup.

Next.js helps you write high performing web apps that render in the browser and on the server.
Built on top of [React](https://facebook.github.io/react/), [Babel](https://babeljs.io/) and [webpack](https://webpack.js.org/), it provides essential tools to develop, compile and serve your code without getting in the way once it's time to customize functionality.

Read the [introductory post](https://zeit.co/blog/next "Next.js introduction at zeit.co") or jump ahead to the [Getting Started](#getting-started) guide.

[![Build Status](https://travis-ci.org/zeit/next.js.svg?branch=master)](https://travis-ci.org/zeit/next.js)
[![Coverage Status](https://coveralls.io/repos/zeit/next.js/badge.svg?branch=master)](https://coveralls.io/r/zeit/next.js?branch=master)
[![Slack Channel](https://zeit-slackin.now.sh/badge.svg)](https://zeit.chat)

:warning: **The README on `master` branch might not match that of the [latest stable release](https://github.com/zeit/next.js/releases/latest).**

## Table Of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
<!-- https://github.com/thlorenz/doctoc -->

:construction: t.b.d. :construction:

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Getting Started

To begin with, make sure to create directory and `package.json` inside to start working from.

```bash
mkdir next-project && cd next-project
npm init -y
```

From there it takes __three simple steps__ to set up your app.

### Install the `next` package

First up, install Next.js via [npm](https://npmjs.com/package/next):

```bash
npm install next --save
```

:warning: Make sure you are running  at least the current [Node.js LTS](https://nodejs.org/en/download/) version.

### Setup Tasks

Next, add a few scripts to your `package.json` which will give you some easy commands to _develop, build_ and _start_ your app.

```json
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

### Add a Page

By default Next.js looks for page components inside a `pages` directory, with the root being `index.js`. Just create this file and populate it as follows:

```jsx
// pages/index.js
export default () => (
  <div>Welcome to Next.js!</div>
)
```

Finally run `npm run dev` and navigate your web browser to `http://localhost:3000`.

:information_source: _For a production ready build run `npm run build` and serve it via `npm start`._ :boom:

> So far, we get:
>
> - Automatic transpilation and bundling (with [Babel](https://babeljs.io/) and [webpack](https://webpack.js.org/))
> - Hot code reloading
> - Server rendering and indexing of `./pages/`

To see how simple this is, check out the [sample app - Nextgram](https://github.com/zeit/nextgram).

## Usage Guide

### CSS

#### Built-in CSS Support

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/basic-css">Basic CSS</a></li></ul>
</details></p>

We bundle [styled-jsx](https://github.com/zeit/styled-jsx) to provide support for isolated scoped CSS. The aim is to support _shadow CSS_ resembling of [Web Components](https://en.wikipedia.org/wiki/Web_Components), which unfortunately [do not support server-rendering and are JS-only](https://github.com/w3c/webcomponents/issues/71).

```jsx
export default () => (
  <div>
    Hello world

    <p>The following Styles are scoped to this component, yay!</p>

    <style jsx>{`
      p {
        color: blue;
      }
      div {
        background: red;
      }
      @media (max-width: 600px) {
        div {
          background: blue;
        }
      }
    `}</style>
  </div>
)
```

Check out [styled-jsx](https://github.com/zeit/styled-jsx) for more info on what's possible.

#### CSS-in-JS

<p><details>
  <summary>
    <b>Examples</b>
    </summary>
  <ul><li><a href="./examples/with-styled-components">Styled components</a></li><li><a href="./examples/with-styletron">Styletron</a></li><li><a href="./examples/with-glamor">Glamor</a></li><li><a href="./examples/with-cxs">CXS</a></li><li><a href="./examples/with-aphrodite">Aphrodite</a></li><li><a href="./examples/with-fela">Fela</a></li></ul>
</details></p>

We support any existing _CSS-in-JS_ solution. The simplest one is inline styles:

```jsx
export default () => (
  <p style={{ color: 'red' }}>hi there</p>
)
```

:information_source: _To use more sophisticated CSS-in-JS solutions, you typically have to implement style flushing for server-side rendering. We enable this by allowing you to define a [custom `<Document>`](#user-content-custom-document) that wraps each page._

### Static File Serving (e.g.: images)

Create a folder called `static` in your project's root directory. This is where all your static files will live. From within your code you may reference these files at the `/static/:filename` route.

```jsx
export default () => (
  <img src="/static/my-image.png" />
)
```

:warning: We currently discourage the use of importing static assets as JS modules. As this would likely need a [custom webpack config](#customizing-webpack-config), that gets applied to the client bundle only, we can't guarantee the server and client rendered versions of a page to look the same.

### Populating `<head>`

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/head-elements">Head elements</a></li>
    <li><a href="./examples/layout-component">Layout component</a></li>
  </ul>
</details></p>

We expose a component `<Head>` for appending elements to the `<head>` of the page.

```jsx
import Head from 'next/head'

export default () => (
  <div>
    <Head>
      <title>My page title</title>
      <meta name="viewport" content="initial-scale=1.0, width=device-width" />
    </Head>
    <p>Hello world!</p>
  </div>
)
```

:information_source: Contents of `<head>` declared outside of [`_document.js`](#custom-document) get cleared upon unmounting the component. Make sure each page completely defines what it needs in `<head>`, without making assumptions about what other pages added.

### Fetching Intial Data

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/data-fetch">Data fetch</a></li></ul>
</details></p>

Next.js allows for page components to declare a static [async](https://zeit.co/blog/async-and-await) method `getInitialProps`. It may fetch and return initial `props` for pages.

Just export a page component with a `class` that `extends React.Component` like you would when using [React's built-in lifecycle methods](https://facebook.github.io/react/docs/react-component.html#the-component-lifecycle).

```jsx
// pages/hello.js
import React from 'react'

export default class Hello extends React.Component {

  static async getInitialProps ({ req }) {
    return req
      ? { userAgent: req.headers['user-agent'] }
      : { userAgent: navigator.userAgent }
  }

  render () {
    return <div>
      Hello {this.props.userAgent}
    </div>
  }
}
```

We expect `getInitialProps` to return a JavaScript plain `Object` which then populates the page's `props`.

:information_source: _`getInitialProps` will execute on the server or the client&mdash;never both. For the initial page load it will be executed on the server only. When navigation to a different route using the [routing APIs](#client-side-routing) it will be executed on the client only._

#### Function Arguments for `getInitialProps`

`getInitialProps` receives a `context` object with the following properties:

- `pathname: string` &ndash; Path section of URL.
- `query: Object` &ndash; Query string section of URL, parsed as an object.
- `req?: Object` &ndash; HTTP request object (server only).
- `res?: Object` &ndash; HTTP response object (server only).
- `xhr?: Object` &ndash; `XMLHttpRequest` object (client only).
- `err?: Error` &ndash; `Error` object if any error is encountered during rendering.

### Client-Side Routing

#### Using the `<Link>` Component

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/hello-world">Hello World</a></li>
  </ul>
</details></p>

Client-side transitions between routes may be enabled via the exposed `<Link>` component. Consider these two pages:

```jsx
// pages/index.js
import Link from 'next/link'

export default () => (
  <div>Click <Link href="/about"><a>here</a></Link> to read more</div>
)
```

```jsx
// pages/about.js
export default () => (
  <p>Welcome to About!</p>
)
```

<!-- TODO: Document `<Link>` properties. -->
Accepted properties for `<Link>` are as follows:

- `href: string` &ndash; the path to link to.
- `as?: string` &ndash; An optional _decoration_ of the URL. Useful if you configured [custom routes on the server](#custom-server-and-routing).

:information_source: _The `<Link>` component doesn't implicitly render an `<a>` tag. This is so you can choose any element you'd like (e.g. `<button>`). Also, in case it's child is in fact an `<a>` element, the `href` property on `<Link>` gets passed down. This prevents you from having to repeat it._

Client-side routing behaves exactly like the browser:

1. The component is fetched.
2. If it defines [`getInitialProps`](#fetching-initial-data), data is fetched. If an error occurs, [`_error.js`](#error-handling) is rendered.
3. After 1 and 2 complete, [`pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Example_of_pushState()_method) is performed and the new component rendered.

:information_source: _For maximum performance use [`next/prefetch`](#prefetching-pages) to link to and prefetch a page at the same time._

#### Imperatively Using the `url` Property

Each page component receives a `url` property, an object with the following API:

- `pathname: string` &ndash; Current path excluding the query string
- `query: Object` &ndash; Parsed query string. Defaults to `{}`.
- `push(url: string, as?: string): boolean` &ndash; Performs a [`pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Example_of_pushState()_method) call with the given url. Returns `true` on success, else `false`.
- `replace(url: string, as?: string): boolean` &ndash; Performs a [`replaceState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Example_of_replaceState()_method) call with the given url. Returns `true` on success, else `false`.

The `as` parameter for `push` and `replace` corresponds to the equally named [`<Link>` property](#using-the-link-component).

#### Imperatively Using the `Router` Class

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/using-router">Basic routing</a></li>
    <li><a href="./examples/with-loading">With a page loading indicator</a></li>
  </ul>
</details></p>

You may also perform client-side page transitions using `next/router`.

```jsx
import Router from 'next/router'

export default () => (
  <div>Click <span onClick={() => Router.push('/about')}>here</span> to read more.</div>
)
```

Above `Router` object comes with the following API:

- `route: string` &ndash; Current route.
- `pathname: string` &ndash; Current path excluding the query string.
- `query: Object` &ndash; Parsed query string. Defaults to `{}`.
- `push(url: string, as?: string): boolean` &ndash; Performs a [`pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Example_of_pushState()_method) call with the given url. Returns `true` on success, else `false`.
- `replace(url: string, as?: string): boolean` &ndash; Performs a [`replaceState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Example_of_replaceState()_method) call with the given url. Returns `true` on success, else `false`.

The `as` parameter for `push` and `replace` corresponds to the equally named [`<Link>` property](#using-the-link-component).

:information_source: _In order to programmatically change the route without triggering navigation and component-fetching, use `props.url.push` and `props.url.replace` within a component as [described above](#imperatively-using-the-url-property)_.

##### Router Events

You may implement listeners for events being fired inside the Router.

```js
Router.onRouteChangeStart = (url) => {
  console.log('App is changing to: ', url)
}
```

These are the supported event listeners:

- `onRouteChangeStart(url: string): void` &ndash; Called when a route starts to change. Defaults to `null`.
- `onRouteChangeComplete(url: string): void` &ndash; Called when a route changed completely. Defaults to `null`.
- `onRouteChangeError(err: Error & {cancelled: boolean}, url: string): void` &ndash; Called when an error occurs douring a route change. Defaults to `null`.

:information_source: _Here `url` is the URL shown in the browser. If you call `Router.push(url, as)` (or similar), the value of `url` will be the value of `as`._

If you no longer want to listen to an event, simply unset the listener like this:

```js
Router.onRouteChangeStart = null
```

In case loading of a route is canceled (e.g. by clicking two links rapidly in succession), `routeChangeError` will fire. The passed `err` will contain a `cancelled` property set to `true`.

```js
Router.onRouteChangeError = (err, url) => {
  if (err.cancelled) {
    console.log(`Route to ${url} was cancelled!`)
  }
}
```

### Prefetching Pages

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-prefetching">Prefetching</a></li></ul>
</details></p>

Next.js exposes a module that configures a `ServiceWorker` automatically to prefetch pages: `next/prefetch`.

> Since Next.js server-renders your pages, this allows all the future interaction paths of your app to be instant. Effectively Next.js gives you the great initial download performance of a _website_, with the ahead-of-time download capabilities of an _app_. [Read more](https://zeit.co/blog/next#anticipation-is-the-key-to-performance).

#### Using the `<Link>` Component

To prefetch a route substitute your usage of `<Link>` with the default export of `next/prefetch`. For example:

```jsx
// components/navigation.js
import Link from 'next/prefetch'

export default () => (
  <nav>
    <ul>
      <li><Link href='/'><a>Home</a></Link></li>
      <li><Link href='/about'><a>About</a></Link></li>
      <li><Link href='/contact'><a>Contact</a></Link></li>
    </ul>
  </nav>
)
```

When this higher-level `<Link>` component is first used, the `ServiceWorker` gets installed.

The `<Link>` component from `next/prefetch` accepts an additional boolean property `prefetch`. This enables turning off prefetching on a per-`<Link>` basis.

```jsx
<Link href='/contact' prefetch={false}><a>Home</a></Link>
```

#### Imperatively Using `prefetch` Function

Most needs are addressed by using `<Link>`. With the `prefetch` function we also expose an imperative API for advanced usage:

```jsx
// pages/my-page.js
import { prefetch } from 'next/prefetch'

export default ({ url }) => (
  <div>
    <a onClick={ () => setTimeout(() => url.pushTo('/dynamic'), 1000) }>
      Click here and a route transition will happen after 1s...
    </a>
    {
      // but we can prefetch it!
      prefetch('/dynamic')
    }
  </div>
)
```
The API is defined as follows:

- `prefetch(url: string): void` &ndash; Prefetches given url.

### Custom Server and Routing

<p><details>
  <summary><b>Examples</b></summary>
  <ul>
    <li><a href="./examples/custom-server">Basic custom server</a></li>
    <li><a href="./examples/custom-server-express">Express integration</a></li>
    <li><a href="./examples/custom-server-hapi">Hapi integration</a></li>
    <li><a href="./examples/custom-server-koa">Koa integration</a></li>
    <li><a href="./examples/parameterized-routing">Parameterized routing</a></li>
    <li><a href="./examples/ssr-caching">SSR caching</a></li>
  </ul>
</details></p>

Typically you start your next server with `next start`. It's possible, however, to start a server 100% programmatically in order to customize routes, use route patterns, etc.

This example makes `/a` resolve to `./pages/b`, and `/b` resolve to `./pages/a`:

```js
// server.js
const { createServer } = require('http')
const { parse } = require('url')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare().then(() => {
  createServer((req, res) => {
    const parsedUrl = parse(req.url, true)
    const { pathname, query } = parsedUrl

    if (pathname === '/a') {
      app.render(req, res, '/b', query)
    } else if (pathname === '/b') {
      app.render(req, res, '/a', query)
    } else {
      handle(req, res, parsedUrl)
    }
  })
  .listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
```

#### Programmatic API

Create an instance by calling `next` as follows:

- `next(options?: Object): Server` &ndash; Creates a `next/server` instance.

Supported `options` are:

- `dir: string` &ndash; Where the Next.js project is located. Defaults to `'.'`.
- `dev: boolean` &ndash; Whether to launch Next.js in development mode (enables hot reloading). Defaults to `false`.
- `staticMarkup: boolean` &ndash; Whether to render pages to static markup (i.e. call [`ReactDOMServer.renderToStaticMarkup()`](https://facebook.github.io/react/docs/react-dom-server.html#rendertostaticmarkup)). Defaults to `false`.
- `quiet: boolean` &ndash; Whether to hide error messages containing server information. Defaults to `false`.

A `Server`'s API looks like this:

- `getRequestHandler(): () => Promise` &ndash; Returns a `handle` function resolving a given url.
  - `handle(req: Object, res: Object, parsedUrl?: Object): Promise` &ndash; Resolves when requested url is rendered successfully. `req`/`res` correspond to HTTP request/result objects. `parsedUrl` defaults to `url.parse(req.url, true)`.
- `prepare(): Promise` &ndash; Prepares the server for handling requests. Must resolve before calling `handle()` or `render` functions.
- `close(): Promise` &ndash; Shuts down the server.
- `start(port: number, hostname: string): Promise` &ndash; Starts an HTTP server at given `hostname` and `port`.
- `render(req: Object, res: Object, pathname: string, query: Object): Promise` &ndash; Renders a given `pathname`. `req`/`res` correspond to HTTP request/result objects.
- `renderJSON(req: Object, res: Object, page: string, opts: { dir?: string }): Promise` &ndash; Renders a given page (inside `pages/`) as JSON representation. `req`/`res` correspond to HTTP request/result objects. `opts.dir` defaults to `process.cwd()`.
- `renderError(err: Object, req: Object, res: Object, pathname: String, query: Object)` &ndash; [Renders error page](#error-handling) with the given error.
- `renderErrorJSON(err: Object, req: Object, res: Object)` &ndash; Renders error page as JSON representation.

### Custom `<Document>`

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-styled-components">Styled components custom document</a></li></ul>
  <ul><li><a href="./examples/with-amp">Google AMP</a></li></ul>
</details></p>

Pages in `Next.js` skip the definition of the surrounding document's markup. For example, you never include `<html>`, `<body>`, etc. To override that default behavior, you must create a file at `pages/_document.js`, where you can extend the `Document` class:

```jsx
// pages/_document.js
import Document, { Head, Main, NextScript } from 'next/document'

export default class MyDocument extends Document {

  static async getInitialProps (ctx) {
    const props = await Document.getInitialProps(ctx)
    return { ...props, customValue: 'hi there!' }
  }

  render () {
    return (
     <html>
       <Head>
         <style>{`body { margin: 0 } /* custom! */`}</style>
       </Head>
       <body className="custom_class">
         {this.props.customValue}
         <Main />
         <NextScript />
       </body>
     </html>
    )
  }
}
```

The `ctx` object is equivalent to the one received in all [`getInitialProps`](#fetching-data-and-component-lifecycle) hooks, with one addition:

- `renderPage(): {html: string, head: Array[React.Element]}` &ndash; A callback that executes the actual React rendering logic (synchronously). It's useful to decorate this function in order to support server-rendering wrappers like Aphrodite's [`renderStatic`](https://github.com/Khan/aphrodite#server-side-rendering)

### Error handling

#### Custom Error Page

404 and 500 errors are handled both client and server side by a default component `<Error>`. If you wish to override it, export a component from `pages/_error.js`:

```jsx
// pages/_error.js
import React from 'react'

export default class Error extends React.Component {

  static getInitialProps ({ res, xhr }) {
    const statusCode = res ? res.statusCode : (xhr ? xhr.status : null)
    const env = xhr ? 'client' : 'server'

    return { statusCode, client }
  }

  render () {
    return (
      <p>{
        this.props.statusCode
        ? `An error ${this.props.statusCode} occurred on ${this.props.env}`
        : 'An unknown error occurred.'
      }</p>
    )
  }
}
```

As shown above you may receive error information via arguments passed to `getInitialProps`.

#### Using `<Error>` Component

You may wish to render the error page from within a component. For this scenario we expose the `<Error>` component at `next/error`. Consider the following example:

```js
// pages/index.js
import React from 'react'
import ErrorPage from 'next/error'
import fetch from 'isomorphic-fetch'

export default class extends React.Component {

  static async getInitialProps({ query }) {
    const data = await fetch(`/my-endpoint.json?user=${query.user}`)
    return { data }
  }

  render() {
    if(this.props.data.error) {
      return <ErrorPage statusCode={404} />
    } else {
      return <h1>Hello {data.name}</h1>
    }
  }
}
```

`<Error>` accepts the following properties:

- `statusCode?: number` &ndash; The HTTP status code.

### Configuration

Next.js can be configured in multiple ways

#### Configuration In `next.config.js`

For customizing Next.js with advanced behavior, you may create a file `next.config.js` at your project's root.



```javascript
// next.config.js
module.exports = {
  // This file is not going through Babel transformation.
  // So, we write it in vanilla JS
  // (But you could use ES2015 features supported by your Node.js version)

  /* config options here */

}
```

_information_source: _`next.config.js` is a regular Node.js module, not a JSON file. It gets used by the Next server and build phases, and not included in the browser build._

We support the following configuration properties

- `poweredByHeader: boolean` &ndash; Adds the HTTP header `X-Powered-By:` with Next.js version information to server results. Defaults to `true`.
- `webpack: () => Object` &ndash; A function returning a modified webpack configuration object. [Check out the detailed info](#customizing-webpack-config).


#### Customizing webpack Configuration

In order to extend [our usage of webpack](./server/build/webpack.js), you may define a function that extends its config via `next.config.js`.

```js
// next.config.js
module.exports = {
  webpack: (config, { dev }) => {

    /* Perform customizations to config */
    
    // Important: return the modified config
    return config
  }
}
```

:warning: *Warning: Adding loaders to support new file types (css, less, svg, etc.) is __not__ recommended because only the client code gets bundled via webpack and thus it won't work on the initial server rendering. Babel plugins are a good alternative because they're applied consistently between server/client rendering (e.g. [babel-plugin-inline-react-svg](https://github.com/kesne/babel-plugin-inline-react-svg)).*

#### Customizing Babel Config

<p><details>
  <summary><b>Examples</b></summary>
  <ul><li><a href="./examples/with-custom-babel-config">Custom babel configuration</a></li></ul>
</details></p>

In order to extend [our usage of Babel](./server/build/babel/preset.js) you may simply define a `.babelrc` file at the root of your app. This file is optional.

:information_source: _If found, we're going to consider it the *source of truth*, therefore it needs to define what next needs as well, which is the `next/babel` preset. This is designed so that you are not surprised by modifications we could make to our Babel configuration._

Here's an example `.babelrc` file:

```json
{
  "presets": [
    "next/babel",
    "stage-0"
  ],
}
```

## Production Deployment

To deploy, instead of running `next`, you probably want to build ahead of time. Therefore, building and starting are separate commands:

```bash
next build
next start
```

For example, to deploy with [`now`](https://zeit.co/now) a `package.json` like follows is recommended:

```json
{
  "name": "my-app",
  "dependencies": {
    "next": "latest"
  },
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

Then just run `now` and enjoy!

:information_source: _We recommend putting `.next` in `.npmignore` or `.gitignore`. Otherwise, use `files` or `now.files` to opt-into a whitelist of files you want to deploy (and obviously exclude `.next`)._

## FAQ

<details>
  <summary>Is this production ready?</summary>
  Next.js has been powering `https://zeit.co` since its inception.

  We’re ecstatic about both the developer experience and end-user performance, so we decided to share it with the community.
</details>

<details>
  <summary>How big is it?</summary>

The client side bundle size should be measured in a per-app basis.
A small Next main bundle is around 65kb gzipped.

</details>

<details>
  <summary>Is this like `create-react-app`?</summary>

Yes and No.

Yes in that both make your life easier.

No in that it enforces a _structure_ so that we can do more advanced things like:
- Server side rendering
- Automatic code splitting

In addition, Next.js provides two built-in features that are critical for every single website:
- Routing with lazy component loading: `<Link>` (by importing `next/link`)
- A way for components to alter `<head>`: `<Head>` (by importing `next/head`)

If you want to create re-usable React components that you can embed in your Next.js app or other React applications, using `create-react-app` is a great idea. You can later `import` it and keep your codebase clean!

</details>

<details>
  <summary>How do I use CSS-in-JS solutions?</summary>

Next.js bundles [styled-jsx](https://github.com/zeit/styled-jsx) supporting scoped css. However you can use a CSS-in-JS solution in your Next app by just including your favorite library [as mentioned before](#css-in-js) in the document.
</details>

<details>
  <summary>What syntactic features are transpiled? How do I change them?</summary>

We track V8. Since V8 has wide support for ES6 and `async` and `await`, we transpile those. Since V8 doesn’t support class decorators, we don’t transpile those.

See [this](https://github.com/zeit/next.js/blob/master/server/build/webpack.js#L79) and [this](https://github.com/zeit/next.js/issues/26)

</details>

<details>
  <summary>Why a new Router?</summary>

Next.js is special in that:

- Routes don’t need to be known ahead of time
- Routes are always lazy-loadable
- Top-level components can define `getInitialProps` that should _block_ the loading of the route (either when server-rendering or lazy-loading)

As a result, we were able to introduce a very simple approach to routing that consists of two pieces:

- Every top level component receives a `url` object to inspect the url or perform modifications to the history
- A `<Link />` component is used to wrap elements like anchors (`<a/>`) to perform client-side transitions

We tested the flexibility of the routing with some interesting scenarios. For an example, check out [nextgram](https://github.com/zeit/nextgram).

</details>

<details>
<summary>How do I define a custom fancy route?</summary>

We [added](#custom-server-and-routing) the ability to map between an arbitrary URL and any component by supplying a request handler.

On the client side, we have a parameter call `as` on `<Link>` that _decorates_ the URL differently from the URL it _fetches_.
</details>

<details>
<summary>How do I fetch data?</summary>

It’s up to you. `getInitialProps` is an `async` function (or a regular function that returns a `Promise`). It can retrieve data from anywhere.
</details>

<details>
  <summary>Can I use it with GraphQL?</summary>

Yes! Here's an example with [Apollo](./examples/with-apollo).

</details>

<details>
<summary>Can I use it with Redux?</summary>

Yes! Here's an [example](./examples/with-redux)
</details>

<details>
<summary>What is this inspired by?</summary>

Many of the goals we set out to accomplish were the ones listed in [The 7 principles of Rich Web Applications](http://rauchg.com/2014/7-principles-of-rich-web-applications/) by Guillermo Rauch.

The ease-of-use of PHP is a great inspiration. We feel Next.js is a suitable replacement for many scenarios where you otherwise would use PHP to output HTML.

Unlike PHP, we benefit from the ES6 module system and every file exports a **component or function** that can be easily imported for lazy evaluation or testing.

As we were researching options for server-rendering React that didn’t involve a large number of steps, we came across [react-page](https://github.com/facebookarchive/react-page) (now deprecated), a similar approach to Next.js by the creator of React Jordan Walke.

</details>

## Roadmap

Our Roadmap towards 2.0.0 [is public](https://github.com/zeit/next.js/wiki/Roadmap#nextjs-200).

## Contributing

Feel free to discuss any issue you're having with Next.js.
You may [open an issue]() or [join our Slack Channel]()

For development, please see our [contributing.md](./contributing.md)

## Authors

- Naoyuki Kanezawa ([@nkzawa](https://twitter.com/nkzawa)) – ▲ZEIT
- Tony Kovanen ([@tonykovanen](https://twitter.com/tonykovanen)) – ▲ZEIT
- Guillermo Rauch ([@rauchg](https://twitter.com/rauchg)) – ▲ZEIT
- Dan Zajdband ([@impronunciable](https://twitter.com/impronunciable)) – Knight-Mozilla / Coral Project
- Tim Neutkens ([@timneutkens](https://github.com/timneutkens))