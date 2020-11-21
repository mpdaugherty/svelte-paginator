<script>
 export let endpoint = ((currentPage, perPage) => { return { numItems: 0, items: [] } })
 export let currentPage = 1
 export let perPage = 40
 export let numPageLinks = 9 // Ideally odd for the design to balance & at least 5 to have enough space

 let items = []
 let numItems = 0

 let state = 'loading'

 let pageLinks = []

 $: numPages = Math.ceil(numItems / perPage)
 $: {
   const linkArr = []

   linkArr.push({ text: '&laquo;', page: currentPage > 1 ? currentPage - 1 : null })

   if (numPages <= numPageLinks) {
     // If there are more than enough slots for the total number of pages, just list all the pages
     for (let i = 1; i <= numPages; i++) {
       linkArr.push({ text: i, page: i})
     }
   } else {
     // If there are less than 7 total links, show only the first and last pages on the sides
     // and leave the remaining slots for the middle pages. If there's more than 7 links, make
     // this look a little nicer by showing the first & second and the last two pages.
     //
     // Example with 7 links:
     // 1 … 4 5 6 … 10
     //
     // Example with 9 links:
     // 1 2 … 4 5 6 … 9 10
     const numEachSideLinks = numPageLinks > 7 ? 2 : 1
     const numMidLinks = numPageLinks - 2 * numEachSideLinks

     // Put in the left-side links
     for (let i = 1; i <= numEachSideLinks; i++) {
       linkArr.push({ text: i, page: i})
     }

     // We want to always display the current page and as many pages next to it as possible.
     const numLeftMidPages = Math.ceil((numMidLinks - 3) / 2)
     const numRightMidPages = Math.floor((numMidLinks - 3) / 2)

     // Figure out where to put "..." into the list of pages. This may happen in one or two locations
     // (not 0, since that is handled above where numPages <= numPageLinks)
     const hasLeftEllipsis = currentPage - numLeftMidPages > numEachSideLinks + 2
     const hasRightEllipsis = currentPage + numRightMidPages < numPages - numEachSideLinks - 1

     if (hasLeftEllipsis) {
       linkArr.push({ text: '...' })
     }

     // Start & end for middle pages
     const midStartPage = hasLeftEllipsis ?
                          (hasRightEllipsis ? currentPage - numLeftMidPages : numPages - numEachSideLinks - numMidLinks + 2) :
                          numEachSideLinks + 1
     const midEndPage = hasRightEllipsis ?
                        midStartPage + numMidLinks - (hasLeftEllipsis ? 3 : 2) :
                        numPages - numEachSideLinks

     for (let i = midStartPage; i <= midEndPage; i++) {
       linkArr.push({ text: i, page: i })
     }

     if (hasRightEllipsis) {
       linkArr.push({ text: '...' })
     }

     // Put in the right-side links
     for (let i = numPages - numEachSideLinks + 1; i<= numPages; i++) {
       linkArr.push({ text: i, page: i })
     }
   }
   linkArr.push({text: '&raquo;', page: currentPage < numPages ? currentPage + 1 : null })
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
