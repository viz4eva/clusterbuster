<script>
  import VisuGrafik from "./visu/VisuGrafik.svelte";
  import VisuExplanation from "./visu/VisuExplanation.svelte";
  import VisuCustomFact from "./visu/VisuCustomFact.svelte";
  import ClusterExplanation from "./ClusterExplanation.svelte";
  import { slide } from "svelte/transition";
  export let type;
  export let visu_id;
  export let data;
  export let input;
  export let path;
  export let expertMode;
  export let generate;
  let extendedPath = "cluster";
  let extended = false;
  let extensionType;
  switch (type) {
    case 0:
      extensionType = 4;
      break;
    case 2:
      extensionType = 5;
      break;
    case 3:
      extensionType = 6;
      break;
  }

  const extendingQuestions = {
    text: {
      0: "Wie erfolgreich ist das Thema des Textes im Vergleich zu anderen?",
      2: "Ist diese Zielgruppe repräsentativ für Texte mit ähnlichem Thema?",
      3: "Waren thematisch ähnliche Texte zur gleichen Zeit erfolgreich?",
    },
  };


  function switchPathExtension() {
    extended = extended ? false : true;
  }
</script>

<div class="visu-wrapper">

  <div class="left vis-grid-elem">
    {#if type == 1}
      <ClusterExplanation {data} /><br />
    {/if}
    <VisuGrafik {type} {visu_id} {data} {input} {path} />
  </div>
  <div class="right vis-grid-elem explanation">
    <VisuExplanation {type} {expertMode} />
  </div>

  <div class="custom vis-grid-elem">
    {#if type == 1 && data.lollipop.length <= 1}
      <p>
        Diesem Text wurden keine anderen thematisch ähnlichen Artikel
        zugeordnet. Deswegen ist die Analyse dieser Grafik in diesem Fall nicht
        sinnvoll und es wird keine Erklärung erzeugt.
      </p>
    {:else if type == 3 && data.areaPage.length <= 2}
      <p>
        Weil eine detaillierte Analyse des Zeitverlaufs ist noch nicht sinnvoll
        ist, wurde keine Erklärung erzeugt.
      </p>
    {:else if generate}
      <VisuCustomFact {type} on:running on:listening {input} />
    {/if}
  </div>

  <div class="extension vis-grid-elem">
    {#if !extended && type != 1}
      <button on:click={switchPathExtension}
        >{extendingQuestions[path][type]}</button
      >
    {/if}
    {#if extended}
      <div class="input-doc">
        {extendingQuestions[path][type]}
      </div>
      <div class="clusterpath-wrapper" transition:slide>
        <VisuGrafik {type} {visu_id} {data} {input} path={extendedPath} />
        {#if generate}
        <div class="clusterpath-fact">
          <VisuCustomFact type={extensionType} on:running on:listening {input}/>
        </div>
        {/if}
      </div>
      {#if type == 2 || type == 3}
        <ClusterExplanation {data} />
      {/if}
    {/if}

    {#if type == 0}
      <ClusterExplanation {data} />
    {/if}
  </div>
</div>

<style>
  .visu-wrapper {
    border: solid 2px #eee;
    padding: 20px;
    margin-bottom: 20px;
    margin-top: 10px;
    border-radius: 10px;
    display: grid;
    grid-template-areas:
      "expl expl expl expl "
      "vis vis vis vis"
      "custom custom custom custom"
      "ext ext ext ext";
    width: 95%;
  }

  .left {
    grid-area: vis;
    border-radius: 10px;
    padding: 20px;
    overflow-x: scroll;
  }

  .right {
    grid-area: expl;
  }

  .custom {
    grid-area: custom;
    padding: 40px;
  }

  .extension {
    grid-area: ext;
  }

  .vis-grid-elem {
    margin-left: 10px;
  }

  .clusterpath-wrapper {
    border: dashed #ddd 2px;
    padding: 20px;
    border-radius: 10px;
    width: 53vw;
    overflow-x: scroll;
  }

  .clusterpath-fact {
    padding: 20px;
  }

  .input-doc {
    margin-bottom: 10px;
  }
</style>
