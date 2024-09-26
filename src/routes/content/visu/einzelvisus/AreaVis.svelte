<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";
  export let data;
  export let attribute;
  export let color;
  export let input;

  let lineChart;
  const height = 200;
  const width = 750;
  const margin = 20;
  const tickCount = Math.min(data.length, 10);

  onMount(() => {
    //Scales
    const yScale = d3.scaleLinear(
      [0, d3.max(data.map((el) => el[attribute]))],
      [150, 20]
    );

    const timeScale = d3.scaleTime(
      [
        new Date(d3.min(data.map((el) => el.date))),
        new Date(new Date(d3.max(data.map((el) => el.date)))),
      ],
      [0, 700]
    );

    //Axis
    d3.select(lineChart)
      .append("g")
      .attr("transform", `translate(${margin * 2},0)`)
      .call(d3.axisLeft(yScale).tickValues(generateIntegerTicks(yScale,5)));
    //.call((g) => g.select(".domain").remove());

    const timeAxis = d3
      .select(lineChart)
      .append("g")
      .attr("transform", `translate(${margin * 2},${height - margin * 2 - 10})`)
      .call(
        d3
          .axisBottom(timeScale)
          .ticks(d3.timeDay.every(Math.ceil(data.length / tickCount)))
          .tickFormat(d3.timeFormat("%d.%m"))
      );

    // Create the horizontal axis generator, called at startup and when zooming.
    const xAxis = (g, x) =>
      g.call(
        d3
          .axisBottom(x)
          .ticks(width / 80)
          .tickSizeOuter(0)
      );

    // The area generator, called at startup and when zooming.
    const areaGenerator = (data, x) => {
      return d3
        .area()
        .x((d) => x(new Date(d.date)) + margin * 2)
        .y0(yScale(0))
        .y1((d) => yScale(d[attribute]))(data);
    };

    const dotPositionGenerator = (input, x) => {
      return x(new Date(input)) + margin * 2;
    };

    //zoom behavior
    const zoom = d3
      .zoom()
      .scaleExtent([1, 10])
      .extent([
        [width/2, 0],
        [width - margin, height],
      ])
      .translateExtent([
        [margin/2, -Infinity],
        [width - margin, Infinity],
      ])
      .on("zoom", zoomed);

    //clip-path crops the area
    const clip = Math.floor(Math.random() * 1000) + 1;
    d3.select(lineChart)
      .append("clipPath")
      .attr("id", clip)
      .append("rect")
      .attr("x", margin * 2)
      .attr("y", 0)
      .attr("width", 800)
      .attr("height", 200);

    //create outline for area
    const outline = d3
      .select(lineChart)
      .append("path")
      .attr("clip-path", `url(#${clip})`)
      .attr("fill", color)
      .attr("d", areaGenerator(data, timeScale));

    const dot = d3.select(lineChart)
      .append("circle")
      .attr("cx", dotPositionGenerator(input, timeScale))
      .attr("cy", yScale(0))
      .attr("r", 5)
      .attr("fill", "red")
      .attr("stroke","black")
      .attr("clip-path", `url(#${clip})`);

    //redraw the area and the x axis.
    function zoomed(event) {
      const xz = event.transform.rescaleX(timeScale);
      outline.attr("d", areaGenerator(data, xz));
      dot.attr("cx", dotPositionGenerator(input, xz));
      timeAxis.call(xAxis, xz);
    }

    d3.select(lineChart).call(zoom);
  });

  function generateIntegerTicks(scale, maxTicks) {
            const domain = scale.domain();
            const start = Math.ceil(domain[0]);
            const end = Math.floor(domain[1]);
            const range = end - start;

            if (range <= 0) return [start];  // Only one integer in range

            // Calculate step to ensure at most `maxTicks` ticks
            let step = Math.ceil(range / maxTicks);
            
            // If the domain is smaller than maxTicks, ensure ticks are within the domain
            let ticks = d3.range(start, end + 1, step);
            if (ticks.length > maxTicks) {
                step += 1;
                ticks = d3.range(start, end + 1, step);
            }
            console.log(ticks);
            return ticks;
        }
</script>

<svg bind:this={lineChart} {width} {height}> </svg>
