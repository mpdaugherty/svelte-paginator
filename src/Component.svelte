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
     const numEachSideLinks = numPageLinks > 7 ? 2 : 1
     const numMidLinks = numPageLinks - 2 * numEachSideLinks

     // Put in the left-side links
     for (let i = 1; i <= numEachSideLinks; i++) {
       linkArr.push({ text: i, page: i})
     }

     // We want to always display the current page and as many pages next to it as possible.
     const numSidePages = (numMidLinks - 3) / 2

     // Figure out where to put "..." into the list of pages. This may happen in one or two locations
     // (not 0, since that is handled above where numPages <= numPageLinks)
     const hasLeftEllipsis = currentPage - numSidePages > numEachSideLinks + 1
     const hasRightEllipsis = currentPage + numSidePages < numPages - numEachSideLinks - 1

     if (hasLeftEllipsis) {
       linkArr.push({ text: '...' })
     }

     // Start & end for middle pages
     const midStartPage = hasLeftEllipsis ?
                          (hasRightEllipsis ? Math.floor(currentPage - numSidePages) : numPages - numEachSideLinks - numMidLinks + 2) :
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
