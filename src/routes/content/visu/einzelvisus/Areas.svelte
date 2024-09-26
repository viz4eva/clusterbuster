<script>
  import AreaVis from "./AreaVis.svelte";
  export let path;
  export let data;
  export let input;

  //berechne gleitenden Durschnitt und filtere Cluster-Building-Tage im Mai für Size heraus
  data = data.map((elem, index) => {
    if (path === "cluster") {
      return {
        date: elem.date,
        visits: gleitenderDurschnitt(index, "visits"),
        abos: gleitenderDurschnitt(index, "abos"),
        size: elem.date.includes("2023-05") ? 0 : elem.size,
      };
    }
    return {
      date: elem.date,
      visits: gleitenderDurschnitt(index, "visits"),
      abos: gleitenderDurschnitt(index, "abos"),
      size: elem.date.includes("2023-05") ? 0 : elem.size,
    };
  });
  console.log(data);

  function gleitenderDurschnitt(index, category) {
    if (index > 2) {
      const lastThree = data.slice(index - 2, index + 1);
      const sum = lastThree.reduce((accumulator, currentValue) => {
        return accumulator + currentValue[category];
      }, 0);
      const avg = sum / 3;
      return avg;
    }
    return data[index][category];
  }
</script>

<div class="chart-wrapper">
  {#if data.length > 1}
    <details open>
      <summary>Wann gab es Visits? (seit 2023)</summary>
      <AreaVis {data} attribute={"visits"} color={"#627fa6"} {input}/>
    </details>
    {#if data.reduce((accumulator, currentValue) => accumulator + currentValue.abos, 0) > 0}
      <details open>
        <summary>Wann gab es Bestellungen? (seit 2023)</summary>
        <AreaVis {data} attribute={"abos"} color={"hotpink"} {input}/>
      </details>
    {:else}
      <p>Dieser Text hat keine Abos erzielt.</p>
    {/if}
    {#if path === "cluster"}
      <details open>
        <summary>Wann kamen Texte zum Themenbereich hinzu? (ab Juni 2023 erfasst)</summary>
        <AreaVis {data} attribute={"size"} color={"lightgreen"} {input}/>
      </details>
    {/if}
  {:else}
    <p>Der Text ist zu neu, um einen zeitlichen Verlauf nachzuvollziehen.</p>
  {/if}
</div>
