<script>
  import TriangleVis from "./TriangleVis.svelte";
  export let data;
  export let path;
</script>

{#if path === "cluster"}
  <h4>Texte zu diesem Thema vs. andere Themen</h4>
  <TriangleVis
    avgVisitLine={data.clusterVsAllClusters.avgVisitLine}
    avgAbosLine={data.clusterVsAllClusters.avgAbosLine}
    visitTriangle={data.clusterVsAllClusters.visitTriangle}
    aboTriangle={data.clusterVsAllClusters.aboTriangle}
  />
{:else}
  <div class="double">
    <div>
      <h4>Dieser Text vs. alle Texte</h4>
      <TriangleVis
        avgVisitLine={data.textVsAll.avgVisitLine}
        avgAbosLine={data.textVsAll.avgAbosLine}
        visitTriangle={data.textVsAll.visitTriangle}
        aboTriangle={data.textVsAll.aboTriangle}
      />
    </div>
    <div>
      <h4>Dieser Text vs. Texte zu ähnlichen Themen</h4>
      <TriangleVis
        avgVisitLine={data.textVsCluster.avgVisitLine}
        avgAbosLine={data.textVsCluster.avgAbosLine}
        visitTriangle={data.textVsCluster.visitTriangle}
        aboTriangle={data.textVsCluster.aboTriangle}
      />
    </div>
  </div>
{/if}

<style>
  .double {
    display: grid;
    grid-template-columns: 350px 350px;
  }
</style>
