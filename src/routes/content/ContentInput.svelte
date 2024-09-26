<script>
  import { createEventDispatcher, onMount } from "svelte";
  import SimilarClusters from "./SimilarClusters.svelte";
  import { last_cluster, last_input } from "../stores.js";
  import { get } from "svelte/store";
  import Spinner from "./Spinner.svelte";
  //import { PUBLIC_API_URL } from "$env/static/public";
  export let input_type;
  export let block_id;
  export let expertMode;
  let inputNeeded = true;
  let loadingData = false;
  let loadingSuggestions = false;
  let input = "nix";
  const dispatch = createEventDispatcher();
  let articleSuggestions = [];

  let isSticky = false;

  let topPerformersData = [];

  onMount(() => {
    function handleScroll() {
      const element = document.getElementById("stickyElement");
      const { top } = element.getBoundingClientRect();
      isSticky = top <= 10;
    }
    window.addEventListener("scroll", handleScroll);

    return () => {
      window.removeEventListener("scroll", handleScroll);
    };
  });

  function clusterDataFetchFromLink(input) {
    //example data for demo prototype
    let exampleArticle = "kinder";
    if (
      input ===
      "https://www.zeit.de/sinn/2024-05/falschparker-melden-privatperson-abschleppwagen-ordnungsamt"
    ) {
      exampleArticle = "denunziant";
    }
    loadingData = true;
    setTimeout(() => {
      fetch(`./example_data/${exampleArticle}/viz_data.json`)
        .then((res) => res.json())
        .then((res) => {
          dispatch("dataFound", {
            data: res,
            input: input,
          });
          //save last loaded cluster in stores for similar cluster search
          last_cluster.set(res);
          last_input.set(input);
          loadingData = false;
        });
    }, 3000);

    //dynamic fetching is omitted for demo
    // loadingData = true;
    // fetch(`${PUBLIC_API_URL}/clusterdata4links?page=${input}`, {
    //   mode: "cors",
    // })
    //   .then((res) => res.json())
    //   .then((res) => {
    //     console.log(res);
    //     if (!res.reply) {
    //       dispatch("dataFound", {
    //         data: res,
    //         input: input,
    //       });
    //       //save last loaded cluster in stores for similar cluster search
    //       last_cluster.set(res);
    //       last_input.set(input);
    //       loadingData = false;
    //     } else {
    //       dispatch("noData");
    //       loadingData = false;
    //     }
    //   });
  }

  function handleLinkInput() {
    inputNeeded = false;
    //Input aus Zeile holen und darauf basierend Cluster holen
    input = document.querySelector(`#text-input-${block_id}`).value;
    if (input) {
      clusterDataFetchFromLink(input);
    }
  }

  function handleSimilarClusterInput(event) {
    inputNeeded = false;
    const focusText = event.detail.focusText;
    input = focusText;
    const focusCluster = parseInt(event.detail.cluster);
    const loadedData = get(last_cluster);
    //checken, ob geladener Cluster mit dem neuen übereinstimmt

    console.log(focusCluster, loadedData.cluster_id);
    clusterDataFetchFromLink(focusText);
  }
</script>

{#if loadingData}
  <div class="input-doc">
    <a href={input} target="_blank">{input.split("www.")[1]}</a>
  </div>
  <Spinner message={"Lade passende Daten..."} />
{:else if inputNeeded}
  {#if input_type === 0}
    {#if articleSuggestions.length == 0 && topPerformersData.length == 0}
      {#if loadingSuggestions}
        <Spinner message={"Lade Vorschläge..."} />
      {:else}
        <button disabled>FragZON Top 5</button>
        <button disabled>Meistbesucht letzte 90 Tage</button>
        <button disabled>Meistabonniert letzte 90 Tage</button>
        <div>
          <!--Text-Input for dynamic version was replaced by select-input featuring two example articles-->
          <!-- <input
            type="text"
            placeholder="Füge einen ZON-Link ein"
            id="text-input-{block_id}"
          /> -->
          <p>Diese Demo-Version umfasst eine Auswahl von zwei Artikeln.</p>
          <select id="text-input-{block_id}">
            <option
              value="https://www.zeit.de/sinn/2024-05/falschparker-melden-privatperson-abschleppwagen-ordnungsamt"
              >zeit.de/sinn/2024-05/falschparker-melden-privatperson-abschleppwagen-ordnungsamt</option
            >
            <option
              value="https://www.zeit.de/hamburg/2023-12/randale-zentrale-hamburg-kinder-rassismus-adultismus-workshops"
              >zeit.de/hamburg/2023-12/randale-zentrale-hamburg-kinder-rassismus-adultismus-workshops</option
            >
          </select>
          <button on:click={handleLinkInput} disabled={loadingData}
            >Suchen</button
          >
        </div>
      {/if}
    {/if}
    {#if articleSuggestions.length != 0}
      <div class="input-doc">FragZON Top 5</div>
      {#each articleSuggestions as suggestion}
        <button
          on:click={() => {
            articleSuggestions = [];
            input = suggestion;
            inputNeeded = false;
            clusterDataFetchFromLink(suggestion);
          }}
          disabled={!suggestion.startsWith("https://www.zeit.de")}
        >
          {suggestion}</button
        >
      {/each}
    {/if}
    {#if topPerformersData.length != 0}
      <div class="input-doc">Erfolgreichste Texte der letzten 3 Monate</div>
      {#each topPerformersData as suggestion}
        <button
          on:click={() => {
            articleSuggestions = [];
            input = suggestion;
            inputNeeded = false;
            clusterDataFetchFromLink(suggestion);
          }}
        >
          {suggestion}</button
        >
      {/each}
    {/if}
  {:else if input_type === 1}
    <SimilarClusters on:clusterInput={handleSimilarClusterInput} {expertMode} />
  {/if}
{:else}
  <div class="input-doc {isSticky ? 'sticky' : ''}" id="stickyElement">
    <a href={input} target="_blank">{input.split("www.")[1]}</a>
  </div>
{/if}

<style>
  /* input[type="text"] {
    border: none;
    border-radius: 30px;
    width: 80%;
    height: 3vh;
    padding: 7px;
    margin-bottom: 20px;
    margin-top: 20px;
    box-shadow: 1px 1px 1px #bbb inset;
  } */

  select {
    border: none;
    border-radius: 30px;
    width: 80%;
    height: 4vh;
    padding: 7px;
    margin-bottom: 20px;
    margin-top: 20px;
    background-color: #fff;
  }

  button {
    margin-top: 10px;
  }

  .input-doc {
    margin-bottom: 5px;
  }

  .sticky {
    position: sticky;
    top: 10px;
  }
</style>
