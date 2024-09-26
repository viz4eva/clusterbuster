<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";

  export let visu_id;
  export let path;
  export let rawData;
  let bandChart;

  let totalVisits;
  let totalAbos;
  const dummyData = [
    {
      segment: "flyby",
      visits: 2000,
      abos: 100,
      color: "grey",
    },
    {
      segment: "brandlover",
      visits: 250,
      abos: 20,
      color: "orange",
    },
    {
      segment: "deepdivers",
      visits: 200,
      abos: 10,
      color: "green",
    },
    {
      segment: "casualreaders",
      visits: 1750,
      abos: 40,
      color: "blue",
    },
    {
      segment: "fastmovers",
      visits: 800,
      abos: 130,
      color: "red",
    },
  ];
  let bandData; //Daten, die sich bei Interaktion updaten

  let hasAbos = false;

  const width = 400;
  let height = 180;
  const margin = 20;
  const colorScale = d3.scaleOrdinal(
    ["flyby", "casual_reader", "brand_lover", "deep_diver", "fast_mover"],
    ["grey", "blue", "orange", "green", "red"]
  );
  onMount(() => {
    console.log(rawData);
    bandData = rawData;
    if (
      rawData.reduce(
        (accumulator, currentValue) => accumulator + currentValue.abos,
        0
      ) > 0
    ) {
      hasAbos = true;
      height = 300;
    }

    buildBandChart();
  });

  function buildBandChart() {
    d3.selectAll(`.rect_${visu_id}_${path}`).remove();
    d3.selectAll(`.text_${visu_id}_${path}`).remove();

    sumUpValues();
    const visitScale = d3.scaleLinear([0, totalVisits], [0, width]);
    const aboScale = d3.scaleLinear([0, totalAbos], [0, width]);

    const band = d3.select(bandChart);

    const visitGroups = band
      .selectAll()
      .data(bandData)
      .join(function (enter) {
        return enter
          .append("g")
          .transition()
          .duration(500)
          .delay(function (d, i) {
            return i * 50;
          })
          .ease(d3.easeCubicOut)
          .attr("opacity", 1);
      });

    visitGroups
      .append("rect")
      .attr("y", 0)
      .attr("height", 80)
      .attr("fill", (d) => colorScale(d.segment))
      .attr("rx", 5)
      .classed(`rect_${visu_id}_${path}`, true)
      .on("mouseover", (e, d) => {
        handleMouseOver(e);
      })
      .on("mouseleave", (e, d) => {
        handleMouseleave(e);
      })
      .transition()
      .duration(500)
      .delay(function (d, i) {
        return i * 50;
      })
      .ease(d3.easeCubicOut)
      .attr("width", (d) => visitScale(d.visits) - 1)
      .attr("x", (d, i) => visitScale(computeStart(i, "visits")) + 1);

    visitGroups.append("title").text((d) => d.visits + " Visits");

    band
      .append("text")
      .attr("x", 1)
      .attr("y", 100)
      .html(`Anteile der Segmente an ${totalVisits} Visits`)
      .classed(`text_${visu_id}_${path}`, true);

    if (hasAbos) {
      const aboGroups = band
        .selectAll()
        .data(bandData)
        .join(function (enter) {
          return enter
            .append("rect")
            .transition()
            .duration(500)
            .delay(function (d, i) {
              return i * 50;
            })
            .ease(d3.easeCubicOut)
            .attr("opacity", 1)
            .attr("width", (d) => aboScale(d.abos) - 1)
            .attr("x", (d, i) => aboScale(computeStart(i, "abos")) + 1);
        })
        .attr("y", 150)
        .attr("height", 80)
        .attr("fill", (d) => colorScale(d.segment))
        .attr("rx", 5)
        .on("mouseover", (e, d) => {
          handleMouseOver(e);
        })
        .on("mouseleave", (e, d) => {
          handleMouseleave(e);
        })
        .classed(`rect_${visu_id}_${path}`, true);

      aboGroups.append("title").text((d) => d.abos + " Abos");

      band
        .append("text")
        .attr("x", 1)
        .attr("y", 250)
        .html(`Anteile der Segmente an ${totalAbos} Abos`)
        .classed(`text_${visu_id}_${path}`, true);

      aboGroups.append("title").text((d) => d.abos + " Abos");
    } else {
      band
        .append("text")
        .attr("x", 1)
        .attr("y", 150)
        .html(`Dieser ${path} hat keine Abos erzielt.`)
        .classed(`text_${visu_id}_${path}`, true);
    }
  }

  function updateSegmentSelection() {
    const checkBoxes = document.querySelectorAll(
      `.checkbox_${visu_id}_${path}`
    );
    const selected = [];
    checkBoxes.forEach((checkbox) => {
      if (checkbox.checked) {
        selected.push(checkbox.name);
      }
    });
    bandData = rawData.filter((segmentData) =>
      selected.includes(segmentData.segment)
    );
    buildBandChart();
  }

  function computeStart(index, category) {
    const startName = "start_" + category;
    if (index == 0) {
      bandData[index].startName = 0;
      return 0;
    }
    const start = bandData[index - 1][category] + bandData[index - 1].startName;
    bandData[index].startName = start;
    return start;
  }

  function sumUpValues() {
    totalVisits = bandData.reduce(function (a, b) {
      return a + b.visits;
    }, 0);

    totalAbos = bandData.reduce(function (a, b) {
      return a + b.abos;
    }, 0);
  }

  function getLabels(segment) {
    if (segment === "flyby") {
      return "Flyby";
    }
    if (segment === "brand_lover") {
      return "Brand Lover";
    }
    if (segment === "deep_diver") {
      return "Deep Diver";
    }
    if (segment === "casual_reader") {
      return "Casual Reader";
    }
    if (segment === "fast_mover") {
      return "Fast Mover";
    }
  }

  function handleMouseOver(e) {
    d3.select(e.currentTarget).style("opacity", 0.7);
  }

  function handleMouseleave(e) {
    d3.select(e.currentTarget).style("opacity", 1);
  }
</script>

<div class="double-col">
  <svg bind:this={bandChart} id="band-vis" {width} {height}></svg>

  <div id={"checkboxGroup_" + visu_id + "_" + path} class="checkBoxGroup">
    {#each rawData as segmentData}
      <input
        checked
        type="checkbox"
        name={segmentData.segment}
        class={"checkbox_" + visu_id + "_" + path}
        id={"checkbox_" + segmentData.segment + "_" + visu_id + "_" + path}
        style="color:red;"
        on:change={() => updateSegmentSelection()}
      />
      <label
        for={segmentData.segment}
        style="color:{colorScale(segmentData.segment)};"
        >{getLabels(segmentData.segment)}</label
      >
      <br />
    {/each}
  </div>
</div>

<style>
  .checkBoxGroup {
    border: solid 1px #ddd;
    padding: 10px;
    border-radius: 10px;
  }

  .double-col {
    display: grid;
    grid-template-columns: 70% 30%;
  }
</style>
