<script>
  import { slide } from "svelte/transition";
  export let data;

  let showExplanation = false;
</script>

{#if showExplanation}
  <div class="cluster-explanation" transition:slide>
    <p>
      Die Texte handeln vom Thema: <span class="bold"
        >{data.clusterLabel[0].label}</span
      >
    </p>
    <p>Gemeinsame Schlüsselwörter:
      <span style="font-style:italic"
        > {data.clusterLabel[0].keywords.map(
          (el) => (el = " " + el)
        )}</span
      >
    </p>
    Anzahl: {data.cluster.length}
    <div class="article-list">
      {#each data.cluster as page}
        <div class="articlelist-elem">
          <a href={page} target="_blank">{page.page.split("www.")[1]}</a>
        </div>
      {/each}
    </div>
    <details>
      <summary
        >Wie werden "ähnliche Texte" oder "Texte eines Themas" bestimmt?</summary
      >
      <p>
        Alle ZON-Texte werden regelmäßig einem Clustering unterzogen. Das
        bedeutet, dass Texte mit ähnlichen Themen, in einer Gruppe, einem
        sogenannten Cluster, zusammengefasst werden. <br> Diese Cluster können
        ziemlich groß werden (mehrere Tausend Texte) oder auch sehr klein
        bleiben. Bei speziellen Themen oder sehr neuen Texten ist es manchmal nur ein
        einzelner Text. <br><br> Der Cluster, dem der ausgewählte Text zugeordnet wurde,
        ist die Grundlage für die Daten, die dir angezeigt werden wenn du in
        diesem Tool "ähnliche Texte" oder "Texte zu diesem Thema" analysierst.
      </p>
    </details>
    <button on:click={() => (showExplanation = false)}>Erklärung ausblenden</button>
  </div>
{:else}
  <button on:click={() => (showExplanation = true)}
    >Mit welchen ähnlichen Texten wird hier verglichen?</button
  >
{/if}

<style>
  .article-list {
    height: 20vh;
    overflow-y: scroll;
    border: solid #ddd 1px;
    border-radius: 10px;
    padding: 10px;
    margin-top: 10px;
    margin-bottom: 10px;
  }

  .articlelist-elem {
    padding-bottom: 5px;
  }

  summary {
    font-weight: 700;
    margin-bottom: 20px;
  }

  .cluster-explanation {
    padding: 20px;
  }

  .bold {
    font-weight: 700;
  }
</style>
