<script>
  import { onMount } from "svelte";
  import Intro from "./Intro.svelte";
  import Contentblock from "./Contentblock.svelte";
  import { last_cluster } from "./stores";
  //import { PUBLIC_API_URL } from "$env/static/public";

  let expertMode = false;
  let generate = true;
  let generationFixed = false;

  onMount(() => {
    //create Thread for GPT Assistant: omitted for the demo
    //fetch(`${PUBLIC_API_URL}/createThread`).then((res) => console.log(res));
  });

  let has_loaded_cluster;
  last_cluster.subscribe((currValue) => {
    has_loaded_cluster = currValue;
    generationFixed = currValue === "" ? false : true;
  });

  let contentBlocks = [
    {
      block_id: Math.floor(Math.random() * 1001),
      input_type: 0,
    },
  ];

  //0 -> Link-Input; 1 -> Cluster-Input
  function addContentblock(input_type) {
    contentBlocks = [
      ...contentBlocks,
      {
        block_id: Math.floor(Math.random() * 1001),
        input_type: input_type == 0 ? 0 : 1,
      },
    ];

    //create new Thread: omitted for the demo
    //fetch(`${PUBLIC_API_URL}/createThread`).then((res) => console.log(res));
  }
</script>

<div class="app-wrapper">
  <h1>Visual Analytics Stories</h1>
  <input
    type="checkbox"
    id="expert-mode"
    on:change={() => (expertMode = expertMode ? false : true)}
  />
  <label for="generate">Erklärungen ausblenden</label>
  <!--  {#if !generationFixed}
    <input
      type="checkbox"
      id="generate"
      checked={generate}
      on:change={() => (generate = generate ? false : true)}
    />
    <label for="generate">Generiere Erklärungen (Wartezeit nötig)</label>
    <br />
  {/if} -->
  <Intro {expertMode} />
  {#each contentBlocks as contentBlock, i (contentBlock)}
    <Contentblock
      block_id={contentBlock.block_id}
      input_type={contentBlock.input_type}
      {expertMode}
      {generate}
    />
  {/each}
  {#if contentBlocks.length < 10}
    {#if has_loaded_cluster}
      <button on:click={() => addContentblock(1)}
        >Zeig mir ähnliche Texte</button
      >
      <button on:click={() => addContentblock(0)}
        >Zeig mir einen anderen Text</button
      >
    {/if}
  {:else}
    <p>Du hast die maximale Anzahl an Content-Blöcken erreicht.</p>
  {/if}
</div>

<style>
  .app-wrapper {
    margin: 3vw;
    margin-top: 3vw;
  }

  :root {
    --font-body: "Lato", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
    --color-text: #111;
    --color-select: #b6c2c6;
    --flyby: grey;
    --casual_reader: blue;
    --deep_diver: green;
    --fast_mover: red;
    --brand_lover: orange;
    font-family: var(--font-body);
    color: var(--color-text);
  }

  :global(button) {
    padding: 10px;
    border-radius: 30px;
    border: none;
    background-color: #ddd;
    margin-top: 5px;
    /*Hover-Effekt für Buttons!*/
  }

  :global(button:hover) {
    background-color: var(--color-select);
  }

  :global(.flyby) {
    color: var(--flyby);
  }

  :global(.casual_reader) {
    color: var(--casual_reader);
  }

  :global(.deep_diver) {
    color: var(--deep_diver);
  }

  :global(.fast_mover) {
    color: var(--fast_mover);
  }

  :global(.brand_lover) {
    color: var(--brand_lover);
  }

  :global(summary):hover {
    color: rgb(87, 87, 233);
  }
</style>
