# svelte-paginator

An unopinionated paginator component for Svelte that will paginate with any backend.

# Development

There is an accompanying test project at [svelte-paginator-test](https://github.com/mpdaugherty/svelte-paginator-test). That project will both test installing this npm module & allow you to set up a dev server that automatically reloads as you update your code.

```shell
git clone git@github.com:mpdaugherty/svelte-paginator.git
git clone git@github.com:mpdaugherty/svelte-paginator-test.git

cd svelte-paginator
npm install

cd ../svelte-paginator-test
npm install
npm run dev
```

Then visit [http://localhost:5000](http://localhost:5000)

## Project structure

### `src/Component.svelte`

This is the file that defines the `Paginator` component. As with all Svelte components, this file is divided into three sections, `<script>`, `<style>`, and the element definition itself.

### [svelte-paginator-test](https://github.com/mpdaugherty/svelte-paginator-test)`/src/App.svelte`

This is an example page that imports & uses `Paginator`. Modify this to quickly test your work.

## Issues & Dev Process

Issues are tracked with Github Issues. When completing issues, please develop on a branch and create a pull request linked to the issue you are working on.

For issue discussions, generally use comments on the Github issues so we have documentation of the decisions we've made - but SMS or email is fine as well if you need a response quickly.
