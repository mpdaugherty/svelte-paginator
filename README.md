# svelte-paginator

An unopinionated paginator component for Svelte.

![Svelte-Paginator Example](https://github.com/mpdaugherty/svelte-paginator/raw/main/README_images/example.png "Svelte Paginator Example")

## Example

```svelte
<script>
 import Paginator from 'svelte-paginator'

 // Create test data
 let letters = '1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('')

 // Function to that loads the test data
 const loadLetters = (page, perPage) => {
   const start = perPage * (page-1)
   const end = start + perPage
   return {
     items: letters.slice(start, end), // The items to display for `page`
     numItems: letters.length // The total number of items available across all pages
   }
 }
</script>

<Paginator loadItems={loadLetters} let:items let:loading>
  <!-- Write code here for whatever you want to do with items, e.g. a list, a table, etc. -->

  <div>
    {#if loading}
      Loading...
    {:else}
      {#each items as letter}
        {letter}&nbsp;
      {:else}
        None
      {/each}
    {/if}
  </div>
</Paginator>
```

## Usage

### Required

There are two required elements for using `svelte-paginator`.

#### `async loadItems(page, perPage)`

`loadItems` should calculate an object of the form

```javascript
{
  item: ['...'], // an array of items to display on this page
  numItems: 99 // the total number of items available
}
```

If you're loading these from a server, `loadItems` can also be an async function or return a promise that resolves to the object.

#### `let:items`

`svelte-paginator` doesn't define anything to do with the items that you're paginating. That's up to you.

To make use of this, add `let:items` to your paginator instance. `items` will be an array of items to display on the current page (it's returned from `loadItems()` above).

### Optional

| Parameter | Definition | Example |
| --- | --- | --- |
| `perPage` | How many items to display per page; defaults to 40 | `<Paginator loadItems perPage={4}>` |
| `numPageLinks` | How many links to display (does not include left & right buttons). Minimum 5. Defaults to 9. | `<Paginator loadItems numPageLinks={7}>` |
| `currentPage` | The current / initial page to load. Defaults to 1. | `<Paginator loadItems bind:currentPage>` |
| `bind:reset` | Exposed function that lets you reset the paginator. | `<Paginator loadItems bind:reset>` |
| `let:loading` | Within the component slot, the value is true if actively loading | `<Paginator loadItems let:loading>{#if loading}...{/if}</Paginator>` |

#### Example with all options

You can see this in action at https://github.com/mpdaugherty/svelte-paginator-test

```svelte
<script>
 import Paginator from 'svelte-paginator'

 let letters = '1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('')
 const loadLetters = async (page=1, perPage=10) => {
   await new Promise(resolve => setTimeout(resolve, 1500)) // Simulate a delay, e.g. loading from a server
   const start = perPage * (page-1)
   const end = start + perPage
   return {
     items: letters.slice(start, end),
     numItems: letters.length
   }
 }

 let reset = null
 const switchToGreek = () => {
   letters = 'αβγδεζηθικλμνξοπρστυφχψωΑΒΓΔΕΖΗΘΙΚΛΜΝΞΟΠΡΣΤΥΦΧΨΩ'
   reset()
 }
</script>

<button on:click={switchToGreek}>Switch to Greek</button>

<Paginator loadItems={loadLetters} perPage={4} numPageLinks={12} currentPage={2} let:items let:loading bind:reset>
  <div>
    {#if loading}
      Loading...
    {:else}
      {#each items as letter}
        {letter}&nbsp;
      {:else}
        None
      {/each}
    {/if}
  </div>
</Paginator>
```

## Styles

If the default styles of `svelte-paginator` are not to your taste, you can override the classes that are used. If you do this, none of the default styles will survive.

Available classes are:

 * `class_button`
 * `class_current_page`
 * `class_button_group`

For example, if you'd like to use Bootstrap classes, you might do something like this:

```svelte
<Paginator loadItems let:items
  class_button="btn btn-outline-secondary"
  class_current_page="btn btn-secondary"
  class_button_group="btn-group">
  ...
</Paginator>
```

# Developing `svelte-paginator`

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

This is an example page that imports & uses `Paginator`. You can modify this to quickly test your work.

## Issues & Dev Process

Issues are tracked with Github Issues. When completing issues, please develop on a branch and create a pull request linked to the issue you are working on.

For issue discussions, generally use comments on the Github issues so we have documentation of the decisions we've made - but SMS or email is fine as well if you need a response quickly.

## Publishing to npm

```shell
npm publish
```
