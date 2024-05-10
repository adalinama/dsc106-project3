<script>
	import { onMount } from 'svelte';
	import * as d3 from 'd3';
	import { scaleSequential, interpolatePiYG } from 'd3';
	 
	let geojsonData;
	let currentYearIndex = 0; // Initial index
	let filteredData = [];
	let endYear;
	
	const yearIntervals = [];

	for (let year = 2000; year < 2022; year++) {
		yearIntervals.push(year);
	}
	 
	let selectedYear = yearIntervals[currentYearIndex];
	$: {selectedYear = yearIntervals[currentYearIndex], console.log(selectedYear);
		let proposedEndYear = selectedYear + 4;
        endYear = proposedEndYear > 2022 ? 2022 : proposedEndYear; // Adjust end year if it exceeds 2022
    }
	 
	const regionalChangeScale = scaleSequential(interpolatePiYG).domain([5, -5]);
	 
	onMount(async () => {
	  	const response = await fetch('output_3.geojson');
	  	geojsonData = await response.json();
	});
	 
	// Reactive statement to log the structure of geojsonData.features (for debugging)
	$: if (geojsonData) console.log(geojsonData.features);
	 
	// Correctly access and log the 'year_interval' of the first feature's properties (for debugging)
	$: if (geojsonData) console.log(geojsonData.features[0].properties.iso_code);
   
	$: filteredData = geojsonData?.features.filter(d => d.properties.year_interval === selectedYear);
	
	$: if (geojsonData) {
	filteredData = geojsonData?.features.filter(d => d.properties.year_interval === selectedYear);
	console.log(`Filtered data count for year ${selectedYear}:`, filteredData.length);
	}
   

	//
	let svg;

  onMount(() => {
    const width = window.innerWidth;
    const height = window.innerHeight;

    // The svg
    svg = d3.select("#my_dataviz")
      .attr("width", width)
      .attr("height", height);

    // Load external data and draw the map
    const projection = d3.json("https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson")
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
	//

	// Adjust the width and height if necessary to fit the map well within the view
	const width = 1200; // This will make the map responsive to the width of the body
  	const height = 700;
	 

	 
	const path = d3.geoPath().projection(projection);
	 
	// Define a transition for the map update
	const transition = d3.transition()
        .duration(550) // Duration in milliseconds
        .ease(d3.easeLinear); // Specify an easing function

	const tooltip = d3.select('#tooltip');
	  
	d3.select('svg')
	 .selectAll('path')
	 .data(filteredData)
	 .enter().append('path')
	 .attr('d', path)
	 .attr('fill', d => {
	   // Access the 'regional_change' for the selected year
	   const change = d.properties.country_energy_cons_change;
	   return regionalChangeScale(change);
	 })
	 .attr('stroke', 'white')
	 .on('mouseover', function(event, d) {
	   tooltip.style('visibility', 'visible')
		.html(`${d.properties.name}
		<br>Region: ${d.properties.Region}
		<br>Regional Change: ${parseFloat(d.properties.regional_change).toFixed(2)}%
		<br>Country Change: ${d.properties.country_energy_cons_change == null ? 'N/A' : parseFloat(d.properties.country_energy_cons_change).toFixed(2)+"%"}
		`)
		
		.style('top', (event.pageY - 10) + 'px')
		.style('left', (event.pageX + 10) + 'px');
	 })
	 .on('mouseout', function() {
	   tooltip.style('visibility', 'hidden');
	 });

	 // Legend setup
	const legendWidth = 300, legendHeight = 20, numberOfSegments = 10;
    const legendData = d3.range(numberOfSegments).map(i => regionalChangeScale.domain()[1] + (i / numberOfSegments) * (regionalChangeScale.domain()[0] - regionalChangeScale.domain()[1]));

	// Assuming you have variables for the overall SVG dimensions
	const svgWidth = 1200;
	const svgHeight = 500;

	// Calculate the right-side margin or padding you prefer
	const marginRight = 20;
	const marginTop = 20;

	// Adjusted legend position
	const legendX = svgWidth - legendWidth - marginRight; // Position legend on the right side
	const legendY = marginTop; // A little padding from the top

	// Create a legend container and position it at the top right corner
	const legend = d3.select('svg').append('g')
		.attr('class', 'legend')
		.attr('transform', `translate(${legendX}, ${legendY})`);

    // Add colored rectangles
    legend.selectAll('rect')
        .data(legendData)
        .enter().append('rect')
        .attr('x', (d, i) => i * (legendWidth / numberOfSegments))
        .attr('y', 0)
        .attr('width', legendWidth / numberOfSegments)
        .attr('height', legendHeight)
        .style('fill', d => regionalChangeScale(d));

    // Add text labels for start, midpoint, and end of the scale
    const textValues = [regionalChangeScale.domain()[1], 0, regionalChangeScale.domain()[0]];
    legend.selectAll('text')
        .data(textValues)
        .enter().append('text')
        .attr('x', (d, i) => i * (legendWidth / (textValues.length - 1)))
        .attr('y', legendHeight + 15)
        .attr('text-anchor', 'middle')
        .text(d => d.toFixed(1));

    // Add a title to your legend
    legend.append('text')
        .attr('x', legendWidth / 2)
        .attr('y', -10)
        .attr('text-anchor', 'middle')
        .style('font-weight', 'bold')
        .text('Primary Energy Consumption Percent Change (%)');
	
	 
	// Reactive drawing of the map based on geojsonData and selectedYear
	$: if (geojsonData && selectedYear) drawMap();

	 </script>

<style>
	input[type="range"] {
	  -webkit-appearance: none; /* Override default CSS styles */
	  appearance: none;
	  width: 50%; /* Full-width */
	  height: 25px; /* Specified height */
	  background: #d2c6c6; /* Grey background */
	  outline: none; /* Remove outline */
	  opacity: 0.7; /* Set transparency (for mouse-over effects) */
	  -webkit-transition: .2s; /* 0.2 seconds transition on hover */
	  transition: opacity .2s;
	}
	
	input[type="range"]:hover {
	  opacity: 1; /* Fully opaque on mouse-over */
	} 
	
	input[type="range"]::-webkit-slider-thumb {
	  -webkit-appearance: none; /* Override default look */
	  appearance: none;
	  width: 60px; /* Set a specific slider handle width */
	  height: 25px; /* Slider handle height */
	  background: #af5e4c; /* Green background */
	  cursor: pointer; /* Cursor on hover */
	}
	

	.slider-message {
		display: inline-block; /* or block if you want it on a new line */
		font-size: 25px;
		color: rgb(173, 0, 0);
		font-style: italic;
		font-weight: bold;
		}

		/* Add this to make sure the slider and the message align on the same line */
		.slider-container {
		align-items: center; /* This will vertically align the slider and message */
		}

	.legand-message {
		font-size: 18px;
		color: rgb(173, 0, 0);
		font-weight: bold;
		}

	.year-display {
		margin-top: 10px;
		font-size: 25px;
		font-weight: bold;
		text-align: center;
	}

	.drawMaps{
		margin-top: 0px;
	}

  </style>
	 
	 <!-- Component Markup -->
	 <div class="slider-container">
		<div class="slider-message">(You can slide the brown bar to change the year)</div>
		<div class="year-display">Year: {selectedYear}</div>
		<input type="range" min="0" max="21" bind:value={currentYearIndex}>
		<div class="legand-message">(Countries that don't have available data are colored black)</div>
	  </div>
	 <svg width="1200" height="1200" class="drawMaps"></svg>