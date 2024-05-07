<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let svg;

  onMount(() => {
    const width = window.innerWidth;
    const height = window.innerHeight;

    // The svg
    svg = d3.select("#my_dataviz")
      .attr("width", width)
      .attr("height", height);

    // Map and projection
    const projection = d3.geoNaturalEarth1()
      .scale(width / 1.3 / Math.PI)
      .translate([width / 2, height / 2]);

    // Load external data and draw the map
    d3.json("https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson")
      .then(data => {
        // Draw the map
        svg.append("g")
          .selectAll("path")
          .data(data.features)
          .enter().append("path")
            .attr("fill", "#69b3a2")
            .attr("d", d3.geoPath()
              .projection(projection)
            )
            .style("stroke", "#fff")
            .on("mouseover", handleMouseOver)
            .on("mouseout", handleMouseOut);
      });
  });

  function handleMouseOver(event, d) {
    d3.select(this)
      .attr("fill", "orange");
  }

  function handleMouseOut(event, d) {
    d3.select(this)
      .attr("fill", "#69b3a2");
  }
</script>

<main>
  <svg id="my_dataviz"></svg>
</main>

<style>
  body, html {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
  }

  svg {
    display: block;
    width: 100%;
    height: 100%;
  }
</style>
