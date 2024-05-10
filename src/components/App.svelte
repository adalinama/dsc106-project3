<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let svg;
  let selectedYear = 2021; // Default selected year
  let countryData = {}; // Object to store internet usage data by country

  onMount(() => {
    const width = 1000;
    const height = 1000;

    // The svg
    svg = d3.select("#my_dataviz")
      .attr("width", width)
      .attr("height", height);

    // Map and projection
    const projection = d3.geoNaturalEarth1()
      .scale(width / 1.3 / Math.PI)
      .translate([width / 2, height / 2]);

    // Load external data and draw the map
    Promise.all([
      d3.json("https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson"),
      d3.csv("https://raw.githubusercontent.com/adalinama/dsc106-project3/main/internet_data_cleaned.csv")
    ]).then(([mapData, internetData]) => {
      // Prepare country data
      prepareCountryData(internetData);

      // Draw the map
      svg.append("g")
        .selectAll("path")
        .data(mapData.features)
        .enter().append("path")
          .attr("fill", d => {
            const countryName = d.properties.name;
            const usagePercentage = countryData[countryName] ? countryData[countryName].percentage : "N/A";
            return getColor(usagePercentage);
          })
          .attr("d", d3.geoPath()
            .projection(projection)
          )
          .style("stroke", "#fff")
          .style("stroke-width", "1px") // Set initial stroke width
          .on("mouseover", handleMouseOver)
          .on("mouseout", handleMouseOut);
    });
  });

  // Function to prepare country data
  function prepareCountryData(internetData) {
    internetData.forEach(d => {
      const countryName = d["Region or Country Name"];
      const year = +d.Year;
      const percentage = +d["% using internet"];
      if (year === selectedYear) {
        countryData[countryName] = { percentage: percentage };
      }
    });
  }

  // Function to get color based on usage percentage
  function getColor(percentage) {
    if (percentage === "N/A") {
      return "gray"; // Color for N/A data
    }
    // Define color scale based on your requirements
    // For example, you can use a sequential color scale
    // to represent low to high internet usage percentages
    // Adjust the color range and domain as needed
    const colorScale = d3.scaleSequential(d3.interpolateBlues)
      .domain([0, 100]); // Assuming percentage ranges from 0 to 100
    return colorScale(percentage);
  }

  function handleMouseOver(event, d) {
    const countryName = d.properties.name;
    const usagePercentage = countryData[countryName] ? countryData[countryName].percentage : "N/A";
    const tooltip = d3.select("body").append("div")
      .attr("class", "tooltip")
      .style("position", "absolute")
      .style("font-family", "Nunito, sans-serif")
      .style("background-color", "white")
      .style("padding", "10px")
      .style("border", "1px solid black")
      .style("border-radius", "5px")
      .html(`<strong>${countryName}</strong><br/>Internet Usage: ${usagePercentage}%`);

    // Highlight the country by changing stroke color
    d3.select(this)
      .style("stroke", "orange")
      .style("stroke-width", "2px");

    // Position tooltip next to the mouse, adjusting for screen edges
    const tooltipWidth = tooltip.node().getBoundingClientRect().width;
    const tooltipHeight = tooltip.node().getBoundingClientRect().height;
    let left = event.pageX + 10;
    let top = event.pageY + 10;
    if (left + tooltipWidth > window.innerWidth) {
      left = window.innerWidth - tooltipWidth - 10;
    }
    if (top + tooltipHeight > window.innerHeight) {
      top = window.innerHeight - tooltipHeight - 10;
    }
    tooltip.style("left", left + "px")
      .style("top", top + "px");
  }

  function handleMouseOut(event, d) {
    // Remove tooltip when mouse leaves the country
    d3.select(".tooltip").remove();

    // Restore stroke color and width
    d3.select(this)
      .style("stroke", "#fff")
      .style("stroke-width", "1px");
  }

  function changeYear(year) {
    selectedYear = year;
    // You can update the map colors here based on the selected year
    // Call prepareCountryData again with the updated year and re-render the map
  }
</script>

<main>
  <h1>World Internet Usage by Year</h1>
  <svg id="my_dataviz">
  </svg>
  <div class="text">
    <p>Sourced from the Department of Economic and Social Affairs of the UN Secretariat</p>
  </div>
  <div class="year-buttons">
    <button class="hoverable" on:click={() => changeYear(2000)}>2000</button>
    <button class="hoverable" on:click={() => changeYear(2005)}>2005</button>
    <button class="hoverable" on:click={() => changeYear(2010)}>2010</button>
    <button class="hoverable" on:click={() => changeYear(2015)}>2015</button>
    <button class="hoverable" on:click={() => changeYear(2019)}>2019</button>
    <button class="hoverable" on:click={() => changeYear(2020)}>2020</button>
    <button class="hoverable" on:click={() => changeYear(2021)}>2021</button>
  </div>
  <div class="rectangle">
    <p style="color:#CFD8E4;">100%</p>
    <p>50%</p>
    <p>0%</p>
  </div>
</main>

<style>
  body, html {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    position: relative; /* Required for absolute positioning */
  }

  h1 {
  text-align: center;
  font-family: "Nunito", sans-serif;
  }

  svg {
    position: relative;
    display: block;
    width: 100%;
    height: 100%;
    margin-left: 90px;
  }

  .year-buttons {
    position: relative;
    bottom: 400px; /* Adjust as needed */
    left: 50%;
    transform: translateX(-50%); /* Center horizontally */
    display: flex;
    justify-content: center; /* Center buttons horizontally */
  }

  .year-buttons button {
    margin: 0 5px; /* Add space between buttons */
  }

  .tooltip {
    font: 14px;
  }

  .hoverable {
      background-color: #D1D1D1;
      border: 2px solid #0D4D91;
      color: black;
      padding: 15px 32px;
      text-align: center;
      display: inline-block;
      font-size: 16px;
      border-radius: 4px;
      transition-duration: 0.5s;
  }

  .hoverable:hover {
    background-color: white;
    box-shadow: 0 12px 16px 0 rgba(0,0,0,0.24), 0 17px 50px 0 rgba(0,0,0,0.19);
  }

p {
  font-family: "Nunito", sans-serif;
  text-align: left;
  position: relative;
}

.rectangle {
  width: 50px; /* Set the width of the rectangle */
  height: 500px; /* Set the height of the rectangle */
  background-image: linear-gradient(#093A7A, white); /* Set the background color of the rectangle */
  border: 1px solid #000;
  margin: auto;

}
.rectangle p {
  text-align: center;
  margin-bottom: 215px;
  margin-top: 5px;
}

</style>
