<script>
  import ContentInput from "./content/ContentInput.svelte";
  import Navigation from "./content/Navigation.svelte";
  import { slide } from "svelte/transition";

  export let block_id;
  export let expertMode;
  export let generate;
  export let input_type = "cluster";
  let foundInput = false;
  let input;
  let noData = false;
  let visu_id;
  let data;
  let currentCluster;

  //if data has been fetched (by Content Input) we can display Navigations
  function handleTextInput(event) {
    data = event.detail.data;
    console.log(data);
    visu_id = data.cluster_id + "_" + block_id;
    input = event.detail.input;
    currentCluster = data.cluster_id;
    foundInput = true;
  }
</script>

<div class="contentblock-wrapper" transition:slide>
  <ContentInput
    on:dataFound={handleTextInput}
    on:noData={() => (noData = true)}
    {input_type}
    {block_id}
    {expertMode}
  />

  {#if foundInput}
    <details open>
      <summary></summary>
      <div class="answer-wrapper">
        <Navigation {data} {input} {visu_id} {expertMode} {generate} {currentCluster} />
      </div>
    </details>
  {:else if noData}
    <div class="answer-wrapper">
      <p>Zu diesem Text wurden keine Daten gefunden.</p>
    </div>
  {/if}
</div>

<style>
  .contentblock-wrapper {
    padding: 40px;
    padding-top: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
    width: 80vw;
    min-width: 800px;
    background-color: #eee;
    box-shadow: 1px 1px 10px #bbb;
  }

  .answer-wrapper {
    background-color: #fff;
    padding: 40px;
    padding-top: 10px;
    border-radius: 10px;
    margin-bottom: 20px;
  }

  :global(.input-doc) {
    background-color: var(--color-select);
    padding: 10px;
    border-radius: 10px;
    width: 90%;
    box-shadow: 1px 1px 2px #bbb;
  }

  summary {
    padding: 10px;
  }
</style>
