<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";
  export let avgVisitLine;
  export let avgAbosLine;
  export let visitTriangle;
  export let aboTriangle;

  const width = 250;
  const height = 450;
  const margin = 25;

  let triangleChart;
  let legend;
  let chartWrapper;
  let tooltip;
  let aboScale;
  let visitScale;
  let colorVisitScale;
  let colorAboScale;
  let scaleID = Math.floor(Math.random() * 100) + 1;

  onMount(() => {
    tooltip = d3
      .select(chartWrapper)
      .append("div")
      .style("opacity", 0)
      .attr("class", "tooltip")
      .style("background-color", "#ddd")
      .style("border-radius", "10px")
      .style("padding", "5px")
      .style("position", "fixed");

    //make scales depending on dimensions
    buildScales(visitTriangle, avgVisitLine, aboTriangle, avgAbosLine);

    //build visualization
    const triangle = d3.select(triangleChart);

    //build gradient for color encoding
    let startColor;
    let endColor;
    if (visitScale(visitTriangle) >= aboScale(aboTriangle)) {
      startColor = colorVisitScale(visitTriangle);
      endColor = colorAboScale(aboTriangle);
    } else {
      startColor = colorAboScale(aboTriangle);
      endColor = colorVisitScale(visitTriangle);
    }
    let defs = triangle.append("defs");

    let gradient = defs
      .append("linearGradient")
      .attr("id", `svgGradient_${scaleID}`)
      .attr("x1", "0%")
      .attr("x2", "0%")
      .attr("y1", "100%")
      .attr("y2", "0%");

    gradient
      .append("stop")
      .attr("class", "start")
      .attr("offset", "0%")
      .attr("stop-color", startColor);

    gradient
      .append("stop")
      .attr("class", "end")
      .attr("offset", "100%")
      .attr("stop-color", endColor);

    //Dreieck
    if (diff(aboScale(aboTriangle), visitScale(visitTriangle)) < 10) {
      triangle
        .append("rect")
        .attr("x", width / 2 - 20)
        .attr("y", aboScale(aboTriangle) - 3)
        .attr("height", 6)
        .attr("width", 40)
        .attr("fill", colorVisitScale(visitTriangle))
        .style("opacity",0.8);
    } else {
      triangle
        .append("path")
        .attr("d", (d) => getTrianglePath(aboTriangle, visitTriangle))
        .attr("fill", `url(#svgGradient_${scaleID})`)
        .style("opacity",0.6);
    }

    //Achsen und Labels
    triangle
      .append("g")
      .attr("transform", `translate(${width - margin * 2 + 5},0)`)
      .call(d3.axisRight(aboScale).ticks());
    //.call((g) => g.select(".domain").remove());

    triangle
      .append("text")
      .attr("text-anchor", "end")
      .attr("x", width)
      .attr("y", 20)
      .attr("font-size", "small")
      .html("Abos");

    triangle
      .append("g")
      .attr("transform", `translate(${margin * 2},0)`)
      .call(d3.axisLeft(visitScale).ticks(5));
    //.call((g) => g.select(".domain").remove());

    triangle
      .append("text")
      .attr("x", 0)
      .attr("y", 20)
      .attr("font-size", "small")
      .html("Visits");

    //Linie für Markierung der Tendenz
    triangle
      .append("line")
      .attr("x1", margin * 2 + 10)
      .attr("y1", visitScale(visitTriangle))
      .attr("x2", width - margin * 2 - 5)
      .attr("y2", aboScale(aboTriangle))
      .attr("stroke", "black")
      .attr("stroke-width", 2);

    //Visits Markierung
    triangle
      .append("rect")
      .attr("x", margin * 2 + 5)
      .attr("y", visitScale(visitTriangle) - 5)
      .attr("height", 10)
      .attr("width", 10)
      .attr("fill", colorVisitScale(visitTriangle))
      .on("mouseover", (event) => handleMouseOver(event,"visits"))
      .on("mousemove", (event) => handleMousemove(event, "visits"))
      .on("mouseleave", (event) => handleMouseleave(event));

    //Abos Markierung
    triangle
      .append("circle")
      .attr("cx", width - margin * 2 - 5)
      .attr("cy", aboScale(aboTriangle))
      .attr("r", 5)
      .attr("fill", colorAboScale(aboTriangle))
      .on("mouseover", (event) => handleMouseOver(event,"orders"))
      .on("mousemove", (event) => handleMousemove(event, "orders"))
      .on("mouseleave", (event) => handleMouseleave(event));

    //Visits Durchschnitt
    triangle
      .append("rect")
      .attr("x", margin * 2 + 5)
      .attr("y", (d) => visitScale(avgVisitLine) - 5)
      .attr("height", 10)
      .attr("width", 10)
      .attr("fill", "grey")
      .on("mouseover", (event) => handleMouseOver(event,"visits"))
      .on("mousemove", (event) => handleMousemove(event, "visits", "avg"))
      .on("mouseleave", (event) => handleMouseleave(event));

    //Abos Durchschnitt
    triangle
      .append("circle")
      .attr("cx", width - margin * 2 - 5)
      .attr("cy", (d) => aboScale(avgAbosLine))
      .attr("r", 5)
      .attr("fill", "grey")
      .on("mouseover", (event) => handleMouseOver(event,"orders"))
      .on("mousemove", (event) => handleMousemove(event, "orders", "avg"))
      .on("mouseleave", (event) => handleMouseleave(event));

    //Linie für Durchschnitte
    triangle
      .append("line")
      .attr("x1", margin * 2 + 10)
      .attr("y1", visitScale(avgVisitLine))
      .attr("x2", width - margin * 2 - 5)
      .attr("y2", aboScale(avgAbosLine))
      .attr("stroke", "grey")
      .attr("stroke-width", 2)
      .attr("stroke-dasharray", 4);

    function getTrianglePath(orders, visits) {
      let path = `M ${width / 2 - 20} ${visitScale(visits)}
                L ${width / 2 + 20} ${visitScale(visits)}
                L ${width / 2} ${aboScale(orders)} Z`;
      return path;
    }
  });

  function buildScales(visitTriangle, avgVisitLine, aboTriangle, avgAbosLine) {
    //select domain based on data dimensions
    let visitDomain;
    let aboDomain;
    if (visitTriangle > avgVisitLine * 2) {
      //console.log("Visits zu hoch");
      //visitDomain = [0, visitTriangle + avgAbosLine]; 
      visitDomain = [avgVisitLine * 2 - visitTriangle, visitTriangle];
    } else {
      //console.log("Visits im Rahmen");
      visitDomain = [0, avgVisitLine * 2];
    }
    
    if (avgAbosLine == 0) {
      //console.log("Abos sind 0");
      aboDomain = visitDomain;
    } else if (aboTriangle > avgAbosLine * 2) {
      //console.log("Abos zu hoch");

     // aboDomain = [0, aboTriangle + avgAbosLine] 
      aboDomain = [avgAbosLine * 2 - aboTriangle, aboTriangle];
    } else {
      //console.log("Abos im Rahmen");
      aboDomain = [0, avgAbosLine * 2];
      //console.log("Baue Abo-Skala mit 2*Abos");
    }

    visitScale = d3.scaleLinear(visitDomain, [height - margin, margin + 20]);
    aboScale = d3.scaleLinear(aboDomain, [height - margin, margin + 20]);

    colorVisitScale = d3
      .scaleSequential()
      .domain(visitDomain)
      .interpolator(d3.interpolatePlasma);
    colorAboScale = d3
      .scaleSequential()
      .domain(aboDomain)
      .interpolator(d3.interpolatePlasma);

    //add legend for sequential scale
    // Generate legend elements
    const legendGradient = d3
      .select(legend)
      .append("defs")
      .append("linearGradient")
      .attr("id", "legendgradient")
      .attr("x1", "0%")
      .attr("x2", "0%")
      .attr("y1", "100%")
      .attr("y2", "0%");

    legendGradient
      .append("stop")
      .attr("offset", "0%")
      .attr("stop-color", colorVisitScale(visitDomain[0]));

    legendGradient
      .append("stop")
      .attr("offset", "50%")
      .attr("stop-color", colorVisitScale(visitDomain[1]/2));

    legendGradient
      .append("stop")
      .attr("offset", "100%")
      .attr("stop-color", colorVisitScale(visitDomain[1]));

    d3.select(legend)
      .append("rect")
      .attr("width", 150)
      .attr("height", height - 62)
      .style("fill", "url(#legendgradient)")
      .attr("transform", `translate(${10},40)`);
  }

  function diff(num1, num2) {
    if (num1 > num2) {
      return num1 - num2;
    } else {
      return num2 - num1;
    }
  }

  function handleMouseOver(event, type, agg) {
    tooltip.style("opacity", 1);
    d3.select(event.currentTarget).style("stroke", "black").style("opacity", 1);
  }

  function handleMousemove(event, type, agg) {
    tooltip
      .html(() => {
        if (type == "visits") {
          if (agg) {
            return "Durchschnittlich " + avgVisitLine.toFixed(1) + " Visits";
          }
          return Math.floor(visitTriangle) + " Visits.";
        } else {
          if (agg) {
            return (
              "Durchschnittlich " + avgAbosLine.toFixed(1) + " Bestellungen"
            );
          }
          return Math.floor(aboTriangle) + " Bestellungen.";
        }
      })
      .style("left", event.clientX + 10 + "px")
      .style("top", event.clientY + 10 + "px");
  }

  function handleMouseleave(event) {
    tooltip.style("opacity", 0);
    d3.select(event.currentTarget).style("stroke", "none").style("opacity", 1);
  }
</script>

<div id="chart-wrapper" bind:this={chartWrapper}>
  <svg bind:this={legend} id="legend" width="20" {height}></svg>
  <svg bind:this={triangleChart} id="triangle-vis" {width} {height}></svg>
</div>

<style>
  svg {
    padding: 0px;
  }
</style>
