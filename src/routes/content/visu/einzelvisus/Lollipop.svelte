<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";

  export let data;
  export let visu_id;
  export let input;

  let lollipopChart;
  let selected = data[0];

  const height = 300;
  const width = 85 * 11;
  const margin = 20;
  const gap = 5;

  //update chart if data changes
  $: data, buildLollipopChart();

  onMount(() => {
    buildLollipopChart();
  });

  function buildLollipopChart() {
    if (data.find((elem) => elem.page === input)) {
      selected = data.find((elem) => elem.page === input);
    } else {
      selected = data[0];
    }
    d3.select(lollipopChart).selectAll("*").remove();

    //wenn nur Nuller-Abos vorhanden sind, funktioniert die Skala nicht
    let noAbos = false;
    if (data.every((elem) => !elem.abos)) {
      noAbos = true;
    }

    let visitScale = d3.scaleLinear(
      [0, d3.max(data.map((el) => el.visits))],
      [(height - margin) / 2 - gap, margin]
    );
    let aboScale;
    if (!noAbos) {
      aboScale = d3.scaleLinear(
        [0, d3.max(data.map((el) => el.abos))],
        [(height - margin) / 2 + gap, height - margin]
      );

      d3.select(lollipopChart)
        .append("g")
        .attr("transform", `translate(${margin * 2},0)`)
        .call(d3.axisLeft(aboScale).ticks(5).tickFormat(d3.format("~s")));
      //.call((g) => g.select(".domain").remove());
    }

    //Axis
    d3.select(lollipopChart)
      .append("g")
      .attr("transform", `translate(${margin * 2},0)`)
      .call(d3.axisLeft(visitScale).ticks(5).tickFormat(d3.format("~s")));

    //selection rectangle
    const selectionRect = d3
      .select(lollipopChart)
      .append("rect")
      .attr("x", positionSelection())
      .attr("y", 0)
      .attr("height", height)
      .attr("width", 10)
      .attr("fill", "#f2efaa")
      .attr("stroke", "#f7eb02")
      .attr("stroke-width", "2px")
      .attr("opacity", "1")
      .attr("id", `select-article-${visu_id}`);

    //Drag-Logik für die Selektion

    let drag = d3.drag().on("drag end", handleDrag);
    d3.select(`#select-article-${visu_id}`).call(drag);

    //build Visit-Lollipops
    const visitRects = d3
      .select(lollipopChart)
      .selectAll()
      .data(data)
      .join(
        function (enter) {
          return enter
            .append("rect")
            .classed("lollipop_rect_" + visu_id, true)
            .transition()
            .duration(500)
            .delay(function (d, i) {
              return i * 0.08;
            })
            .ease(d3.easeCubicInOut)
            .attr("opacity", 1);
        },
        function (update) {
          return update.transition().attr("opacity", 1);
        },
        function (exit) {
          return exit;
        }
      )
      .attr("y", (d) => visitScale(d.visits) - 4)
      .attr("x", (d, i) => margin * 2 + 10 + i * 10 - 4)
      .attr("height", 8)
      .attr("width", 8)
      .attr("fill", (d) => {
        if (d.visits == 0) {
          return "grey";
        }
        return "blue";
      })
      .on("click", (e, d) => {
        selected = d;
        let selectionPos = data.indexOf(
          data.find((elem) => elem.page === d.page)
        );
        let position = selectionPos * 10 + margin * 2 + 5;
        d3.select(`#select-article-${visu_id}`).attr("x", position);
      });

    const VisitLines = d3
      .select(lollipopChart)
      .selectAll()
      .data(data)
      .join(
        function (enter) {
          return enter
            .append("line")
            .classed("lollipop_line_" + visu_id, true)
            .transition()
            .duration(500)
            .delay(function (d, i) {
              return i * 0.08;
            })
            .ease(d3.easeCubicInOut)
            .attr("opacity", 1);
        },
        function (update) {
          return update.transition().attr("opacity", 1);
        },
        function (exit) {
          return exit;
        }
      )
      .attr("y1", (d) => visitScale(d.visits) + 2)
      .attr("x1", (d, i) => margin * 2 + 10 + i * 10)
      .attr("y2", visitScale(0))
      .attr("x2", (d, i) => margin * 2 + 10 + i * 10)
      .attr("stroke", "blue")
      .attr("stroke-width", 2);

    //build Abo-Lollipops
    const aboCircles = d3
      .select(lollipopChart)
      .selectAll()
      .data(data)
      .join(
        function (enter) {
          return enter
            .append("circle")
            .classed("lollipop_circle_" + visu_id, true)
            .transition()
            .duration(500)
            .delay(function (d, i) {
              return i * 0.08;
            })
            .ease(d3.easeCubicInOut)
            .attr("opacity", 1);
        },
        function (update) {
          return update.transition().attr("opacity", 1);
        },
        function (exit) {
          return exit.transition().attr("opacity", 1);
        }
      )
      .attr("r", (d) => {
        if (!d.abos) {
          return 3;
        }
        return 5;
      })
      .attr("cy", (d) => {
        if (noAbos) {
          return (height - margin) / 2 + gap;
        }
        if (d.abos) {
          return aboScale(d.abos);
        }
        return aboScale(0);
      })
      .attr("cx", (d, i) => margin * 2 + 10 + i * 10)
      .attr("fill", (d) => {
        if (!d.abos) {
          return "grey";
        }
        return "hotpink";
      })
      .on("click", (e, d) => {
        selected = d;
        let selectionPos = data.indexOf(
          data.find((elem) => elem.page === d.page)
        );
        let position = selectionPos * 10 + margin * 2 + 5;
        d3.select(`#select-article-${visu_id}`).attr("x", position);
      });

    const aboLines = d3
      .select(lollipopChart)
      .selectAll()
      .data(data)
      .join(
        function (enter) {
          return enter
            .append("line")
            .classed("lollipop_line_" + visu_id, true)
            .transition()
            .duration(500)
            .delay(function (d, i) {
              return i * 0.08;
            })
            .ease(d3.easeCubicInOut)
            .attr("opacity", 1);
        },
        function (update) {
          return update.transition().attr("opacity", 1);
        },
        function (exit) {
          return exit;
        }
      )
      .attr("y1", (d) => {
        if (noAbos) {
          return (height - margin) / 2 + gap;
        }
        if (d.abos) {
          return aboScale(d.abos);
        }
        return aboScale(0);
      })
      .attr("x1", (d, i) => margin * 2 + 10 + i * 10)
      .attr("y2", () => {
        if (noAbos) {
          return (height - margin) / 2 + gap;
        }
        return aboScale(0);
      })
      .attr("x2", (d, i) => margin * 2 + 10 + i * 10)
      .attr("stroke", "hotpink")
      .attr("stroke-width", 2);
  }

  function formatDate(dateString) {
    const date = new Date(dateString);

    // Extract day, month, and year
    const day = date.getDate();
    const monthIndex = date.getMonth();
    const year = date.getFullYear();

    const months = [
      "Januar",
      "Februar",
      "März",
      "April",
      "Mai",
      "Juni",
      "Juli",
      "August",
      "September",
      "Oktober",
      "November",
      "Dezember",
    ];
    const monthName = months[monthIndex];

    const formattedDate = `${day}. ${monthName} ${year}`;

    return formattedDate;
  }

  function positionSelection() {
    let selectionPos = data.indexOf(data.find((elem) => elem.page === input));
    let position = selectionPos * 10 + margin * 2 + 5;
    if (position == 35) {
      position += 10;
    }
    return position;
  }

  function handleDrag(e) {
    if (e.x >= margin * 2 + 5 && e.x <= margin * 2 + data.length * 10 - 4) {
      //Rechteck verschieben
      const newPosition = Math.floor(e.x);
      d3.select(`#select-article-${visu_id}`).attr("x", newPosition);
      //update des selektieren Artikels
      selected = data[Math.floor((newPosition - margin * 2 + 5) / 10) - 1];
    }
  }
</script>

<p id="date">
  {formatDate(data[0].date_first_published) +
    " - " +
    formatDate(data[data.length - 1].date_first_published)}
</p>
<svg bind:this={lollipopChart} id="bubble-vis" {width} {height}> </svg>
<div id="detail-area">
  <div>
    <a href={selected.page} target="_blank">{selected.page}</a> <br />
    <p>
      Veröffentlicht: {formatDate(selected.date_first_published)}
    </p>
    <div class="detail-info">
      <p class="visit-elem">Visits: {selected.visits}</p>
      <p class="abo-elem">
        Bestellungen:
        {#if selected.abos}
          {selected.abos}
        {:else}
          0
        {/if}
      </p>
    </div>
  </div>

  <iframe src={selected.page} title="Ausgewählte Seite" />
</div>

<style>
  #date {
    background-color: #eee;
    padding: 10px;
    border-radius: 10px;
    border: 1px solid lightgrey;
    width: 800px;
  }

  #detail-area {
    background-color: #eee;
    border-radius: 10px;
    padding: 20px;
    display: grid;
    grid-template-columns: 50% 50%;
    gap: 5px;
  }

  iframe {
    border: none;
    margin: 1rem;
    height: 30vh;
    width: 90%;
  }

  .detail-info {
    margin-top: 2rem;
    margin-bottom: 2rem;
    font-size: medium;
    line-height: 2rem;
  }
  a {
    font-size: large;
  }

  .visit-elem {
    border: solid blue;
    background-color: rgb(180, 180, 243);
    padding: 2px;
    border-radius: 10px;
  }

  .abo-elem {
    border: solid hotpink;
    background-color: rgb(243, 164, 203);
    padding: 2px;
    border-radius: 10px;
  }

  svg {
    padding-bottom: 10px;
  }
</style>
