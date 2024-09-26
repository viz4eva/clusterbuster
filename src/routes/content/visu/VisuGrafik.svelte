<script>
  import Areas from "./einzelvisus/Areas.svelte";
  import BandVis from "./einzelvisus/BandVis.svelte";
  import Triangles from "./einzelvisus/Triangles.svelte";
  import Lollipops from "./einzelvisus/Lollipops.svelte";

  export let type;
  export let visu_id;
  export let data;
  export let input;
  export let path;
</script>

<!--Je nach Pfad verschiedene Visualisierungen zeigen-->
{#if path === "cluster"}
  {#if type == 0}
    <Triangles data={data.triangle} {path} />
  {:else if type == 1}
    <Lollipops {data} {input} {visu_id} />
  {:else if type == 2}
    <BandVis {visu_id} rawData={data.band} path={"Cluster"} />
  {:else if type == 3}
    <Areas
      data={data.area}
      path={"cluster"}
      input={data.lollipop.find((el) => el.page === input).date_first_published}
    />
  {/if}
{:else if type == 0}
  <Triangles data={data.triangle} {path} />
{:else if type == 1}
  <Lollipops {data} {input} {visu_id} />
{:else if type == 2}
  <BandVis {visu_id} rawData={data.bandPage} path={"Text"} />
{:else if type == 3}
  <Areas
    data={data.areaPage}
    path={"text"}
    input={data.lollipop.find((el) => el.page === input).date_first_published}
  />
{/if}
