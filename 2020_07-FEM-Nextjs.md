# Frontend Masters Next.js Course with Scott Moss

- Slides: https://hendrixer.github.io/nextjs-course/

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
