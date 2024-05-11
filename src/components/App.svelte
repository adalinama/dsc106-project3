<script>
  import { onMount, setContext } from 'svelte';
  import * as d3 from 'd3';

  
  let svg;
  let selectedYear = 2000; 
  let countryData = {}; 
  onMount(() => {
    const width = window.innerWidth; 
    const height = window.innerHeight; 

    // The svg
    svg = d3.select("#my_dataviz")
  .attr("width", width)
  .attr("height", height)
  .call(d3.zoom().on("zoom", () => {
    svg.attr("transform", d3.event.transform);
  }))
  .append("g");

      

    // Map and projection
    const projection = d3.geoNaturalEarth1()
      .scale(width / 1.3 / Math.PI)
      .translate([width / 2, height / 2]);

    
    // Load external data and draw the map
    updateMapData(selectedYear, projection);
  });

  function updateMapData(year, projection) {
    Promise.all([
      d3.json("https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson"),
      d3.csv(`https://raw.githubusercontent.com/adalinama/dsc106-project3/main/${year}_data.csv`)
    ]).then(([mapData, internetData]) => {
      prepareCountryData(internetData);

      svg.selectAll("path").remove(); // Remove existing map paths

      svg.selectAll("path")
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
          .style("stroke", "black")
          .style("stroke-width", "1px")
          .on("mouseover", handleMouseOver)
          .on("mouseout", handleMouseOut);
    });
  }
    

  function prepareCountryData(internetData) {
    countryData = {}; // Clear existing country data
    internetData.forEach(d => {
      const countryName = d["Country"];
      const year = +d.Year;
      const percentage = +d["% using internet"];
      if (year === selectedYear) {
        countryData[countryName] = { percentage: percentage };
      }
    });
  }

  
  function getColor(percentage) {
    if (percentage === "N/A") {
      return "gray"; 
    }
    const colorScale = d3.scaleSequential(d3.interpolateBlues)
      .domain([0, 100]); 
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

   
    d3.select(this)
      .style("stroke", "black")
      .style("stroke-width", "2.2px");


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
    d3.select(".tooltip").remove();

    
    d3.select(this)
      .style("stroke", "black")
      .style("stroke-width", "1px");
  }
  
  function getClass(year) {
    return selectedYear === year ? 'selected' : '';
  }

  function changeYear(year) {
    selectedYear = year; // Your existing changeYear function
    updateMapData(selectedYear, d3.geoNaturalEarth1().scale(window.innerWidth / 1.3 / Math.PI).translate([window.innerWidth / 2, window.innerHeight / 2]));
  }

  
</script>

<main>
  <div class="title-container">
    <h1>World Internet Usage in {selectedYear}</h1>
  </div>
  <div class="container">
    <svg id="my_dataviz"></svg>
    <div class="legend">
      <h2>% of Population Using the Internet</h2>
      <div class="rectangle">
        <div class="gradient-rectangle">
          <p style="color: #000000; text-align: center;">0%<span class="space"></span>50%<span class="space"></span><span style="color: #D1D8E5;">100%</span></p>
        </div>
      </div>
    </div>
  </div>
  <div class="year-buttons">
    <button class="hoverable {selectedYear === 2000 ? 'selected' : ''}" on:click={() => changeYear(2000)}>2000</button>
    <button class="hoverable {selectedYear === 2005 ? 'selected' : ''}" on:click={() => changeYear(2005)}>2005</button>
    <button class="hoverable {selectedYear === 2010 ? 'selected' : ''}" on:click={() => changeYear(2010)}>2010</button>
    <button class="hoverable {selectedYear === 2015 ? 'selected' : ''}" on:click={() => changeYear(2015)}>2015</button>
    <button class="hoverable {selectedYear === 2019 ? 'selected' : ''}" on:click={() => changeYear(2019)}>2019</button>
    <button class="hoverable {selectedYear === 2020 ? 'selected' : ''}" on:click={() => changeYear(2020)}>2020</button>
    <button class="hoverable {selectedYear === 2021 ? 'selected' : ''}" on:click={() => changeYear(2021)}>2021</button>
  </div>
</main>

<style>
  body, html {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    position: relative; 
  }

  .title-container {
    position: absolute;
    top: 20px; 
    left: 50%;
    transform: translateX(-50%);
    background-color: rgba(255, 255, 255, 0.7); 
    padding: 2px;
    border-radius: 5px;
    text-align: center;
    z-index: 999; 
    pointer-events: none;
    font-family: "Nunito", sans-serif; 
  }

  h1 {
    margin: 0;
    font-family: "Nunito", sans-serif; 
    font-size: 24px;
  }

  .container {
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
  }

  svg {
    flex: 1;
    width: 100%;
    height: 100%;
  }

  .legend {
    width: 300px;
    height: 80px;
    position: absolute;
    bottom: 10px;
    left: 5px;
    background-color: rgba(255, 255, 255, 0.7); 
    padding: 5px;
    border: 1px solid #ccc;
    pointer-events: none;
    font-family: "Nunito", sans-serif; 
  }

  .legend h2 {
    margin-top: 0;
    font-size: medium;
    text-align: center;
  }

  .rectangle {
    width: 300px;
    height: 50px; 
    background-color: #fff;
    border: 1px solid #000;
    margin-bottom: 10px;
    display: flex;
    align-items: center; 
    justify-content: center;
    margin: auto;
  }

  .gradient-rectangle {
    width: 100%;
    height: 100%;
    background-image: linear-gradient(to left, #093A7A, white);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    justify-content: center;
  }

  .gradient-rectangle p {
    text-align: center;
    flex-grow: 1;
    margin-bottom: 105px;
    top: 45px;
  }
  .space {
    margin-right: 95px;
  }

  .year-buttons {
    position: absolute;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    justify-content: center;
  }

  .year-buttons button {
    margin: 0 5px;
  }

  .year-buttons .hoverable.selected {
    background-color: #0D4D91;
    color: white;
  }

  .tooltip {
    font: 14px;
  }

  .hoverable {
    background-color: #D1D1D1;
    border: 2px solid #0D4D91;
    color: black;
    padding: 10px 20px;
    text-align: center;
    display: inline-block;
    font-size: 14px;
    border-radius: 4px;
    transition-duration: 0.5s;
  }

  .hoverable:hover {
    background-color: white;
    box-shadow: 0 8px 12px 0 rgba(0,0,0,0.24), 0 11px 30px 0 rgba(0,0,0,0.19);
  }

  p {
    font-family: "Nunito", sans-serif;
    text-align: left;
    position: relative;
  }

  
</style>
