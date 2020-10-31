<script>
 export let endpoint = ((currentPage, perPage) => { return { numItems: 0, items: [] } })
 export let currentPage = 1
 export let perPage = 40

 let items = []
 let numItems = 0

 let state = 'loading'

 let pageLinks = []

 $: numPages = Math.ceil(numItems / perPage)
 $: {
   const middleMin = Math.max(currentPage - 2, 1)
   const middleMax = Math.min(currentPage + 2, numPages)

   const linkArr = []

   for (let i=middleMin; i<=middleMax; i++) {
     linkArr.push({ text: i, page: i })
   }

   // Add left side
   const leftMax = middleMin <= 3 ? middleMin - 1 : 3
   if (leftMax < middleMin - 1) {
     linkArr.unshift({ text: '...' })
   }

   for (let i=leftMax; i>=1; i--) {
     linkArr.unshift({ text: i, page: i })
   }

   linkArr.unshift({ text: '&laquo;', page: currentPage > 1 ? currentPage - 1 : null })

   // Add right side
   const rightMin = middleMax >= numPages - 2 ? middleMax + 1 : numPages - 2
   if (rightMin > middleMax + 1) {
     linkArr.push({ text: '...' })
   }

   for (let i=rightMin; i<=numPages; i++) {
     linkArr.push({ text: i, page: i })
   }

   linkArr.push({ text: '&raquo;', page: currentPage < numPages ? currentPage + 1 : null })

   pageLinks = linkArr
 }

 // This causes loadItems to be loaded on load, so no need for onMount
 $: currentPage && loadItems()

 async function loadItems () {
   state = 'loading'
   const response = await endpoint(currentPage, perPage)
   numItems = response.numItems
   items = response.items
   state = 'loaded'
 }

 export function reset () {
   items = []
   numItems = 0
   if (currentPage == 1) {
     loadItems()
   } else {
     currentPage = 1
   }
 }
</script>

<style>
 .paginator > .clickable {
   cursor: pointer;
 }
</style>

<div class="btn-group paginator">
  {#each pageLinks as link}
    <button
      type="button"
      class="btn {currentPage == link.page ? 'btn-secondary' : 'btn-outline-secondary'}"
      disabled = {!link.page || link.page == currentPage}
      on:click={() => { if (link.page) { currentPage = link.page } } }>
      {@html link.text}
    </button>
  {/each}
</div>

<slot {items}></slot>

<div class="my-3">
  <div class="btn-group paginator">
    {#each pageLinks as link}
      <button
        type="button"
        class="btn {currentPage == link.page ? 'btn-secondary' : 'btn-outline-secondary'}"
        disabled = {!link.page || link.page == currentPage}
        on:click={() => { if (link.page) { currentPage = link.page } } }>
        {@html link.text}
      </button>
    {/each}
  </div>
</div>
