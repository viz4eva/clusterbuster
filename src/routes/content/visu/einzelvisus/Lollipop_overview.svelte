<script>
  import * as d3 from "d3";
  import { onMount, createEventDispatcher } from "svelte";

  export let data;
  export let visu_id;
  export let input;

  let lollipopChart;
  const dispatch = createEventDispatcher();

  const height = 100;
  const width = 800;
  const margin = 0;
  const gap = 1;

  onMount(() => {
    console.log(data);
    d3.select(`#select-rect-${visu_id}`).call(drag);
    buildLollipopChart();
  });

  const visitScale = d3.scaleSqrt(
    [0, d3.max(data.map((el) => el.visits))],
    [(height - margin) / 2 - gap, margin]
  );

  const aboScale = d3.scaleSqrt(
    [0, d3.max(data.map((el) => el.abos))],
    [(height - margin) / 2 + gap, height - margin]
  );

  d3.select(`#select-rect-${visu_id}`)
    .on("mouseover", (e, d) => {
      d3.select(e.currentTarget).attr("background-color", "red");
    })
    .on("mouseleave", (e, d) => {
      d3.select(e.currentTarget).transition().style("opacity", 1);
    });

  //Drag-Logik für die Selektion
  let drag = d3.drag().on("drag end", handleDrag);
  function handleDrag(e) {
    if (e.x >= 0 && e.x <= width - 85 * (width / data.length) + 10) {
      //Rechteck verschieben
      const newPosition = Math.floor(e.x);
      d3.select(`#select-rect-${visu_id}`).attr("x", newPosition);
      //passende Daten holen bzw. Indizes
      let selectionStart = 0;
      for (let index = 0; index < data.length; index++) {
        if (index * (width / data.length) >= newPosition) {
          selectionStart = index;
          break;
        }
      }
      dispatch("selection", {
        start: selectionStart,
      });
    }
  }

  function buildLollipopChart() {
    const VisitLines = d3
      .select(lollipopChart)
      .selectAll()
      .data(data)
      .join(
        function (enter) {
          return enter
            .append("line")
            .classed("lollipop_" + visu_id, true)
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
      .attr("y1", (d) => visitScale(d.visits))
      .attr("x1", (d, i) => i * (width / data.length))
      .attr("y2", visitScale(0))
      .attr("x2", (d, i) => i * (width / data.length))
      .attr("stroke", "blue")
      .attr("stroke-width", 2);

    const aboLines = d3
      .select(lollipopChart)
      .selectAll()
      .data(data)
      .join(
        function (enter) {
          return enter
            .append("line")
            .classed("line_" + visu_id, true)
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
        if (d.abos) {
          return aboScale(d.abos);
        }
        return aboScale(0);
      })
      .attr("x1", (d, i) => i * (width / data.length))
      .attr("y2", aboScale(0))
      .attr("x2", (d, i) => i * (width / data.length))
      .attr("stroke", "hotpink")
      .attr("stroke-width", 2);
  }

  function positionSelection() {
    let selectionPos = data.indexOf(data.find((elem) => elem.page === input));
    if (selectionPos > data.length - 86) {
      return (selectionPos - 85) * (width / data.length);
    }
    return selectionPos * (width / data.length);
  }
</script>

<svg bind:this={lollipopChart} width="800" {height}>
  <rect
    y="0"
    x={positionSelection()}
    {height}
    width={85 * (width / data.length)}
    fill="#eee"
    stroke="lightgrey"
    id="select-rect-{visu_id}"
  ></rect>
</svg>

<style>
  svg {
    padding-top: 15px;
  }
</style>
