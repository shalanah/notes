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
  functions = "functions" # dir
  publish = "public" # needed this to not error out locally
```

- Run locally `netlify dev`
- See function in action `http://localhost:8888/.netlify/functions/hello`
- Make a better url for our api in `netlify.toml`

```toml
[[redirects]]
  from = "/api/*"
  to = "/.netlify/functions/:splat"
  status = 200 # permanent instead of other 301 redirects
```

## Read event data + use callbacks in functions

Function `functions/res.js`

```js
exports.handler = (event, _context, callback) => {
  console.log(event);
  callback(
    null, // sending back an error
    {
      statusCode: 200,
      body: JSON.stringify({ yep: true })
    }
  );
};
```

JS to hit api

```js
form.onsubmit = e => {
  e.preventDefault();
  formState = "PENDING"; // could be a state
  fetch("/api/res", {
    method: "POST", // no query string
    body: JSON.stringify(formData)
  })
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

### Using with Gatsby

> To run with `netlify dev` make sure you have a `gatsby-config.js` file at the root (can be empty!)

### Sending an email

#### Some Services:

- Mailgun https://www.mailgun.com/pricing
- Sendgrid https://sendgrid.com/pricing/

#### Mailgun

- Add packages `yarn add dotenv mailgun-js`
- `.env`

.env
```
# https://app.mailgun.com/app/account/security/api_keys
MAILGUN_API_KEY=<your API key>

# https://app.mailgun.com/app/sending/domains
# Should start with sandbox....mailgun.org
MAILGUN_DOMAIN=<your domain name>
```

functions/contact.js
```js
require("dotenv").config(); // loads .env file by default can config differently

exports.handler = (event, _context, callback) => {
  const mailgun = require("mailgun-js")({
    apiKey: process.env.MAILGUN_API_KEY,
    domain: process.env.MAILGUN_DOMAIN
  });

  const data = JSON.parse(event.body);

  const email = {
    from: `${data.name} <${data.email}>`,
    to: process.env.SEND_EMAIL,
    subject: "Netlify testing inquiry",
    text: data.message
  };

  mailgun.messages().send(email, function(err, res) {
    callback(err, {
      statusCode: 200,
      body: JSON.stringify(res)
    });
  });
};
```

### Protected Pages w/Gatsby (authorized pages w/Login)
Brand new repo [Repo](https://github.com/shalanah/fem-jam-stack-protected-routes) 
- Help Netlify recognize it is Gatsby by adding an empty file called `gastby-config.js`
- Redirecting dashboard urls to dashboard component with `netlify.toml` (after doing `gatsby-node.js` don't quite understand if the redirects are even necessary)
```toml
[[redirects]]
  from = '/dashboard/*'
  to = '/dashboard'
  status = 200 # Rewrite, keeps url see https://docs.netlify.com/routing/redirects/redirect-options/#http-status-codes.
```
- `yarn init -y`
- `yarn add gatsby react react-dom`
- Creating basic pages (see repo)
- `netlify dev` to get up and running
- Route dashboard to gatsby dashboard elem with `gatsby-node.js`
```js
exports.onCreatePage = ({ page, actions }) => {
  if (page.path.match(/^\/dashboard/)) {
    page.matchPath = "/dashboard/*";
    actions.createPage(page);
  }
};
```
- Create `profile.js` page to be loaded into dashboard
  - Add two links with `import {Link} from 'gatsby'`
  - One is for private stuff
  - Leverage gatsby helper class `activeClassName="active"`
- Create dashboard components for secret and base data and login (logged in vs not) (ie `RouteBase` and `RouteSecret` and `RouteLogin`)
- Use reach router which comes with gatsby
- Dashboard ends up looking like this:
```js
import { navigate } from "gatsby"; // also from reach router
import { Router } from "@reach/router";
...

const Dashboard = ({
  location, // comes with Gatsby, lucky us!
}) => {
  useEffect(() => {
    if (location.pathname.match(/^\/dashboard\/?$/)) {
      navigate("/dashboard/login", { replace: true }); // replace history true get out of redirect looping
    }
  }, []);
  return (
    <Layout>
      <Profile />
      <Router>
        <RouteBase path="/dashboard/base" />
        <RouteSecret path="/dashboard/secret" />
        <RouteLogin path="/dashboard/login" />
      </Router>
    </Layout>
  );
};
```
#### Authentication
Typically with OAuth2, other opts (Node Passport, Octa, AuthZero, Cognito, other integrations)
- We will be using Netlify Identity
- `yarn add react-netlify-identity-widget react-netlify-identity @reach/dialog @reach/tabs @reach/visually-hidden` (first two are not ui related)
- Need to deploy in order to use Netlify auth (also need a site url)
- Add netlify identity context to app (`Layout.js`)

Layout.js
```js
import { IdentityContextProvider } from "react-netlify-identity-widget";
...
return
<IdentityContextProvider
      url={"https://jamstack-intro-auth-shalanah.netlify.com"} // select a url to be deployed to soon!!!
    >...
</IdentityContextProvider>
```

- `netlify init` to deploy to url that you just made up
- Click on `Identity` tab and then `Enable Identity` btn in netlify repo page

