# FEM JAM Stack

See github: [Repo](https://github.com/shalanah/fem-jam-stack-netlify-func) 

## How to use Netlify functions

### Basics

- Install netlify cli (`yarn global add netlify-cli`)
- Make a dir for your functions (`/functions`)
- Make a function (`/functions/hello.js`)

```js
exports.handler = async () => {
  return {
    statusCode: 200,
    body: "hey there :)"
  };
};
```

- Make `netlify.toml`

```toml
[build]
  functions = "functions" // dir
  publish = "public" // needed this to not error out locally
```

- Run locally `netlify dev`
- See function in action `http://localhost:8888/.netlify/functions/hello`
- Make a better url for our api in `netlify.toml`

```toml
[[redirects]]
  from = "/api/*"
  to = "/.netlify/functions/:splat"
  status = 200 // permanent instead of other 301 redirects
```

## Read event data + use callbacks in functions

Function `functions/res.js`

```js
exports.handler = (event, _context, callback) => {
  console.log(event);

  callback(null, { statusCode: 200, body: JSON.stringify({ yep: true }) });
};
```

JS to hit api

```js
form.onsubmit = e => {
  e.preventDefault();
  formState = "PENDING"; // could be a state
  fetch("/api/res", { method: "POST", body: JSON.stringify(formData) })
    .then(res => res.json())
    .then(res => {
      console.log(res);
      formState = "SUCCESS"; // could be a state
    })
    .catch(err => {
      console.error(err);
      formState = "ERROR"; // could be a state
    });
};
```

### Sending an email

#### Services:

- Mailgun https://www.mailgun.com/pricing
- Sendgrid https://sendgrid.com/pricing/
