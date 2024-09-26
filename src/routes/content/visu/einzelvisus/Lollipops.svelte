<script>
  import LollipopOverview from "./Lollipop_overview.svelte";
  import Lollipop from "./Lollipop.svelte";

  export let data;
  export let visu_id;
  export let input;
  

  //handle detail selection in lollipop chart
  const lollipopSelectionSize = 85;
  let lollipopSelectionStart = data.lollipop.indexOf(
    data.lollipop.find((elem) => elem.page === input)
  );
  if (lollipopSelectionStart > data.lollipop.length - lollipopSelectionSize) {
    lollipopSelectionStart -= lollipopSelectionSize - 1;
  }
  function handleLollipopSelection(e) {
    lollipopSelectionStart = e.detail.start;
  }
</script>

{#if data.lollipop.length > lollipopSelectionSize}
  <LollipopOverview
    data={data.lollipop}
    {visu_id}
    on:selection={handleLollipopSelection}
    {input}
  />
  <Lollipop
    data={data.lollipop.slice(
      lollipopSelectionStart,
      lollipopSelectionStart + lollipopSelectionSize
    )}
    {visu_id}
    {input}
  />
{:else}
  <Lollipop data={data.lollipop} {visu_id} {input} />
{/if}
