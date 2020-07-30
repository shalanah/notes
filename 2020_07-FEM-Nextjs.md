# Frontend Masters Next.js Course with Scott Moss

- Slides: https://hendrixer.github.io/nextjs-course/
- Class repo: https://hendrixer.github.io/nextjs-course

## Other
- We get code splitting for free

## What to use...
### Do you only need a single page app?
Use Create React App

### Do you need a static site, like a blog, that's also a SPA?
Use Next.js or Gatsby.

### Need SSR, an API, and all the above?
Use Next.js

## Starting
- `npx create-next-app` OR `yarn add next react react-dom`
- Needs to be hosted on a node platform

## Routing
Routing is easily created just with folder structure
```
- pages
  - index.js
  - notes
    - index.js
    - [id].js // notes/:id
```
- Lightning bolt means it was created on the server and sent down
### Using parameters
```
// useRouter is a hook
// withRouter is for class components
import { useRouter, withRouter } from "next/router";

const Page = () => {
  const router = useRouter();
  const { id } = router.query; // matches file name
  return <div>{`Notes: ${id}`}</div>;
};
```
### Catch all routes
```
- pages
  - test
    - [...params].js
```
```js
import React from "react";
import { useRouter } from "next/router";

const Page = () => {
  const router = useRouter();
  // test/1/2/3/4, params will be [1,2,3,4]
  const { params } = router.query; // AN ARRAY
  return <div>{`Test: ${params}`}</div>;
};

export default Page;
```
### Inclusive/Optional (index) catch all
`[[...params]].js`

## Navigation
### Client side routing
```js
import Link from "next/link";

const Page = () => {
  return (
    <div>
      <p>I am an index page</p>
      <Link href={"/notes"}>Note</Link>
    </div>
  );
};
```
Dynamic
```js
import Link from "next/link";

const Page = () => {
  return (
    <div>
      <p>NOTES!!!</p>
      <Link href={"/notes/[id]"} as={"/notes/1"}>
        Note 1
      </Link>
    </div>
  );
};
```
### Programmatic routing
All of this is on the client

```js
import React from 'react'
import { useRouter } from 'next/router'

export default () => {
  const router = useRouter()
  const id = 2

  return (
    <div>
      <button onClick={e => router.push('/')}>
        Go Home
      </button>
      <button onClick={e => router.push('/user/[id]', `/user/${id}`)}>
        Dashboard
      </button>
    </div>
  )
}
```

## Styling
- Special page for global css `_app.js` (probably also global imports)
```
- pages
  - _app.js
```

### Styled components
```js
import { createGlobalStyle } from "styled-components";

const Global = createGlobalStyle`
  body {
    background: #000;
    color: #fff;
  }
`;
export default function App({ Component, pageProps }) {
  return (
    <>
      <Global />
      <Component {...pageProps} />
    </>
  );
}
```
## Other styling options to look into
- base-ui, theme-ui

## Customizing Next.js config
- `next-config.js`
- Allow to merge smartly with webpack
```js
module.exports = {
  webpack: {
    // webpack config properties
  },
  env: {
    MY_ENV_VAR: process.env.SECRET
  }
}
```
Also can return a function
```js
const {
  PHASE_PRODUCTION_BUILD,
  PHASE_DEVELOPMENT_SERVER,
} = require("next/constants");
module.exports = (phase, { defaultConfig }) => {
  if (phase === PHASE_DEVELOPMENT_SERVER) {
    console.log("I'm in dev");
    return defaultConfig;
  }
  if (phase === PHASE_PRODUCTION_BUILD) {
    console.log("I'm run on the server.");
    return defaultConfig;
  }
  return defaultConfig;
};
```
### Plugins
- Usually start with `with...` like `withOffline`, HOCs
- Install stuff to make plugins easier `yarn add next-env dotenv-load`
- Plugins: https://github.com/vercel/next-plugins
```
const nextEnv = require("next-env");
const dotenvLoad = require("dotenv-load");

// Injects all environment vars into here
// {env: ....}
dotenvLoad();

const withNextEnv = nextEnv();
module.exports = withNextEnv();
```
## Typscript support
- Has support automatically
- Create `tsconfig.json`, run dev and it will tell you to add some packages
- Then change to `.ts` or `.tsx` files

## API Routes
- Just make a folder in pages, add an api folder
- Analagous to pages
```
- pages
  - api
    - index.js
    - notes
      - [id].js
```
Simple example
```js
// Responds to everything PUT/POST everything...
export default (req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'application/json')
  res.end(JSON.stringify({ message: 'hello' }))
}
```
- Make it easier on yourself...`yarn add next-connect cors`
```js
// pages/api/note/index.js
import nc from "next-connect";
import notes from "../../../src/data/data";

const handler = nc()
  .get((req, res) => {
    res.json({ data: notes });
  })
  .post((req, res) => {
    const id = Date.now();
    const note = { ...req.body, id };

    notes.push(note);
    res.json({ data: note });
  });
export default handler;
```

## Data fetching
- All only run on the server
- Good for docs/blogs:
  - `getStaticProps` injects into your component
    - ends up getting exported as JSON file
  - `getStaticPaths` needs params, generate all the doc sites

## Deploying
- vercel makes it the easiest (once installed can use `vc` or `versal`)
- Double check urls, add to `.env