# Nuxt Toolbox Template
![Netlify + Nuxt](https://user-images.githubusercontent.com/43764894/223556133-b99e4822-1da3-4f94-941e-fa95cfb42562.png)

This is a [NuxtJS](https://nuxtjs.org/) v2 project. It is a reference on how to integrate commonly used features within Netlify for Nuxt.js.


## Table of Contents:

- [Build Setup](#build-setup)
- [Deploy to Netlify](#deploy-to-netlify)
  - [Deploy using the Netlify CLI](#deploy-using-the-netlify-cli)
  - [Running Locally](#running-locally)
  - [Deployment Resources](#deployment-resources)
- [Forms](#forms)
  - [Adding a Custom Submission Page](#adding-a-custom-submission-page)
  - [Blocking Bot Spam with a Honeypot Field](#blocking-bot-spam-with-a-honeypot-field)
  - [Forms Resources](#forms-resources)
- [Netlify Functions](#netlify-functions)
  - [Netlify Functions Resources](#netlify-functions-resources)
- [Redirects](#redirects)
  - [Example](#example)
  - [Redirect Resources](#redirect-resources)
- [Testing](#testing)
  - [Included Default Testing](#included-default-testing)
  - [Removing Renovate](#removing-renovate)
  - [Removing Cypress](#removing-cypress)
- [Nuxt Toolbox Template Video Walkthrough](#nuxt-toolbox-template-video-walkthrough)
- [I Want Moar](#i-want-moar)

## Build Setup

```bash
# install dependencies
$ npm install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project
$ npm run generate
```

## Deploy to Netlify

Want to deploy immediately? Click this button

[![Deploy to Netlify Button](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/netlify-templates/nuxt-toolbox)

Clicking this button will create a new repo for you that looks exactly like this one, and sets that repo up immediately for deployment on Netlify.

### Deploy using the Netlify CLI
Click the 'Use the Template' button at the top of this repo. Then install the Netlify CLI tool and run `netlify init`. Or straight from the Netlify CLI, use the `netlify sites:create-template` command in you terminal ([learn more about this command here](https://www.netlify.com/blog/create-a-site-from-a-template-using-the-netlify-cli)) to do the entire flow for you.

```bash
git clone https://github.com/netlify-templates/nuxt-toolbox 

npm install netlify-cli -g # to install the Netlify CLI tool globally

netlify init # initialize a new Netlify project & deploy
```

It will use the information from the included Netlify configuration file, [`netlify.toml`](./netlify.toml), to set up the build command as `npm run generate` to create a static project and locate the build project in the `dist` directory.

The `init` process will also set up continuous deployemnt for your project so that a new build will be triggered & deployed when you push code to the repo (you can change this from your project dashboard: Site Settings/Build & deploy/Continuous Deployment).

You can also use `netlify deploy (--prod)` to manually deploy and `netlify open` to open your project dashboard.

> 💡 we only have so many keystrokes to give, use `ntl` shorthand for `netlify` or make [an alias of your own](https://www.netlify.com/blog/2020/04/12/speed-up-productivity-with-terminal-aliases/) to save hours...of accumulated miliseconds

### Running Locally

You can use `netlify dev` from the command line to access project information like environment variables as well as

- test functions
- test redirects
- share a live session via url with `netlify dev --live`
- [and more](https://cli.netlify.com/netlify-dev/) :)

### Deployment Resources

- [CLI docs](https://docs.netlify.com/cli/get-started/)
- [File-based Netlify Configuration](https://docs.netlify.com/configure-builds/file-based-configuration/)
- [Netlify Dev Overview](https://www.youtube.com/watch?v=RL_gtVZ_79Q&t=812s)
- [Netlify Edge, CDN deployment](https://www.netlify.com/products/edge/)

## Forms

Netlify Forms are a way to wire up your native HTML into being able to seamlessly handle submissions. To get a form working, we need to add two extra things:

1. An extra attribute on the `form` tag, `netlify`

Adding this attribute to our `form` tag will let Netlify know when it loads the page, that it needs to be aware of submissions made through it.

2. A hidden input in the form, `<input type="hidden" name="form-name" value="feedback" />`

Adding this extra input allows our form to be given a name that Netlify can store submissions to. It is a hidden input so your users won't see it but it will pass along the name of our form to Netlify when we submit. In our Netlify Admins site under Forms, we will see our Active Form named `feedback` and all submissions will go there.

With both of those we're ready for folks to give us feedback!

### Adding a custom submission page

While Netlify provides a default submission page for folks, we can customize it as well! With the `action` attribute on the `form` tag we will be able to direct our users to our own page.

In [`components/feedbackForm.vue`](./components/feedbackForm.vue) you'll see the form has the attribute `action="/success"` this will take our user to the custom route `/success` which we created under [`pages/Success.vue`](./pages/Success.vue). As long as the page exists, you can direct folks to it!

### Blocking bot spam with a honeypot field

Many bots scan through webpages and try to see what pages and forms they can get access to. Instead of letting our website receive spam submissions, we can filter out unrelated submissions with a visually-hidden input field.

```html
<p class="hidden">
  <label>
    Don’t fill this out if you’re human: <input name="bot-field" />
  </label>
</p>
```

Since screenreader users will still have this announced, it is important for us to
communicate that this is a field not meant to be filled in.

For this to work we also need to add a `netlify-honeypot` attribute to the form element.

```html
<form netlify netlify-honeypot action="/feedback" method="post"></form>
```

[See it here in the template code.](https://github.com/netlify-templates/nuxt-toolbox/blob/abb24c52738d79bcd11ff8a1bc70e0d2d250e7f1/components/feedbackForm.vue#L3)

### Forms Resources

- [Netlify Forms Setup](https://docs.netlify.com/forms/setup/)
- [Netlify Forms](https://www.netlify.com/products/forms/#main)
- [Netlify Forms - Form Triggered Functions](https://docs.netlify.com/functions/trigger-on-events/)
- [Netlify Forms - Using reCAPTCHA 2](https://docs.netlify.com/forms/spam-filters/#recaptcha-2-challenge)

## Netlify Functions

With Netlify, you can build out server-side code without having to setup and maintain a dedicated server. Inside of our default folder path, [`netlify/functions`](./netlify/functions) you can see an example of the format for JavaScript functions with the [`joke.js`](./netlify/functions/joke.js) file.

The function format expects an `async` function named `handler` to be exported.*

```js
export const handler = async () => {
  // Your code goes in here!
}
```

* *Note: ESLint may report that the async is unnecessary if there is no `await` code within the function, but the `async` is required. Do not delete it.*

This will be the function that will be invoked whenever a client makes a request to the generated endpoints. The endpoint's format is followed as `/.netlify/functions/joke`. So whenever the site is deployed, if you go to `https://<site base url>/.netlify/functions/joke` you will see a random joke!

> Side note: In our example, we're using `import` to include data from another location and `export const const handler` to let our function be consumed by Netlify. We're able to do this because of [esbuild](https://esbuild.github.io). This is a bundler configuration we set in our `netlify.toml` under `[functions]`.

### Netlify Functions Resources

There is quite a bit you can do with these functions, so here are some additional resources to learn more!

- [Netlify Function Format](https://docs.netlify.com/functions/build-with-javascript/#synchronous-function-format)
- [Build Netlify Functions with TypeScript](https://docs.netlify.com/functions/build-with-typescript/)
- [Event-triggered Functions](https://docs.netlify.com/functions/trigger-on-events/)
- [What are Background Functions](https://www.netlify.com/blog/2021/01/07/what-are-background-functions/)
- [Netlify Functions - Examples](https://functions.netlify.com/examples/)
- [Using esbuild as your bundler for new ECMAScript Features](https://www.netlify.com/blog/2021/04/02/modern-faster-netlify-functions/)

## Redirects

In the [`netlify.toml`](./netlify.toml) configuration file there is an example of how to implement redirects. Redirects can be used to do many things from redirecting Single Page Apps more predictably, redirecting based on country/language to leveraging On-Demand Builders for [Distributed Persistant Rendering](https://www.netlify.com/blog/2021/04/14/distributed-persistent-rendering-a-new-jamstack-approach-for-faster-builds/).

In the [`netlify.toml`](./netlify.toml) configuration file there is an example of how to implement redirects. Redirects can be used to do many things from redirecting Single Page Apps more predictably, redirecting based on country/language to leveraging On-Demand Builders for [Distributed Persistant Rendering](https://www.netlify.com/blog/2021/04/14/distributed-persistent-rendering-a-new-jamstack-approach-for-faster-builds/).

In the example we'll be using redirects to have a shorter endpoint to Netlify functions. By default, you call a Netlify function when requesting a path like `https://yoursite.netlify.com/.netlify/functions/functionName`. Instead, we'll redirect all calls from a path including `/api` to call on the Netlify functions. So the path will be `https://yoursite.netlify.com/api/functionName`, a lot easier to remember too.

### Example

```toml
[[redirects]]
from = "/api/*"
to = "/.netlify/functions/:splat"
status = 200
force = true
```

First we create a section in the `.toml` for the redirect using `[[redirects]]`. Each redirect should have this line to start the redirect code, and the redirects will be executed in the order they appear in the `.toml` from top to bottom.

The bare minimum needed is the `from` and `to`, letting the [CDN](https://www.netlify.com/blog/edge-cdn-serverless-cloud-meaaning) know when a route is requested, the `from`, forward it on to another path, the `to`. In the example, we also added an 'Ok' status code, 200, and set the `force` to true to make sure it _always_ redirects from the `from` path.

There are many ways to use redirects. Check out the resouces below to learn more.

### Redirect Resources

- [Redirect syntax and configuration](https://docs.netlify.com/routing/redirects/#syntax-for-the-netlify-configuration-file)
- [Redirect options](https://docs.netlify.com/routing/redirects/redirect-options/)
- [Creating better, more predicatable redirect rules for SPAs](https://www.netlify.com/blog/2020/04/07/creating-better-more-predictable-redirect-rules-for-spas/)
- [Redirect by country or language](https://docs.netlify.com/routing/redirects/redirect-options/#redirect-by-country-or-language)
- [On-Demand Builders](https://docs.netlify.com/configure-builds/on-demand-builders/)

## Testing

### Included Default Testing

We’ve included some tooling that helps us maintain these templates. This template currently uses:

- [Renovate](https://www.mend.io/free-developer-tools/renovate/) - to regularly update our dependencies
- [Cypress](https://www.cypress.io/) - to run tests against how the template runs in the browser
- [Cypress Netlify Build Plugin](https://github.com/cypress-io/netlify-plugin-cypress) - to run our tests during our build process

If your team is not interested in this tooling, you can remove them with ease!

### Removing Renovate

In order to keep our project up-to-date with dependencies we use a tool called [Renovate](https://github.com/marketplace/renovate). If you’re not interested in this tooling, delete the `renovate.json` file and commit that onto your main branch.

### Removing Cypress

For our testing, we use [Cypress](https://www.cypress.io/) for end-to-end testing. This makes sure that we can validate that our templates are rendering and displaying as we’d expect. By default, we have Cypress not generate deploy links if our tests don’t pass. If you’d like to keep Cypress and still generate the deploy links, go into your `netlify.toml` and delete the plugin configuration lines:

```diff
[[plugins]]
  package = "netlify-plugin-cypress"
-  [plugins.inputs.postBuild]
-    enable = true
-
-  [plugins.inputs]
-    enable = false
```

If you’d like to remove the `netlify-plugin-cypress` build plugin entirely, you’d need to delete the entire block above instead. And then make sure sure to remove the package from the dependencies using:

```bash
npm uninstall -D netlify-plugin-cypress
```

And lastly if you’d like to remove Cypress entirely, delete the entire `cypress` folder and the `cypress.config.ts` file. Then remove the dependency using:

```bash
npm uninstall cypress
```

## Nuxt Toolbox Template Video Walkthrough

https://user-images.githubusercontent.com/76533034/156563030-e3c0c2a1-2e3a-43f0-bcf5-5ea891289553.mp4


## I want moar!

If you want to discover more Netlify features via this template, here's a couple of things you could try:

- Add a Netlify plugin, either via [the UI](https://app.netlify.com/plugins) or [`netlify.toml`](https://docs.netlify.com/configure-builds/build-plugins/). For example, why not try the `A11y` plugin to run some accessibility testing on your site!
- [Create environment variables using the CLI](https://www.netlify.com/blog/2021/07/12/managing-environment-variables-from-your-terminal-with-netlify-cli/) to store secret tokens (e.g. API key), and use them in the Netlify function to fetch data from your favorite API.
#   l e t m e s e e  
 