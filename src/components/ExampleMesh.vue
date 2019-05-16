<template>
  <div>
    <h1 class="title">Example Mesh</h1>

    <!-- Define the svg -->
    <svg id="example-mesh" width="500px" height="500px"></svg>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "ExampleMesh",
  mounted() {
    //  When the component is mounted
    //  Get the data from API
    d3.json(
      "https://gist.githubusercontent.com/mbostock/4062045/raw/5916d145c8c048a6e3086915a6be464467391c62/miserables.json"
    ).then(data => {
      console.log("mounted", data);

      //  Define the functions for the drad interactions
      function dragstarted(d) {
        if (!d3.event.active) simulation.alphaTarget(0.3).restart();
        d.fx = d.x;
        d.fy = d.y;
      }

      function dragged(d) {
        d.fx = d3.event.x;
        d.fy = d3.event.y;
      }

      function dragended(d) {
        if (!d3.event.active) simulation.alphaTarget(0);
        d.fx = null;
        d.fy = null;
      }

      function drag() {
        return d3
          .drag()
          .on("start", dragstarted)
          .on("drag", dragged)
          .on("end", dragended);
      }

      //  Define links & nodes from data
      const links = data.links.map(d => Object.create(d));
      const nodes = data.nodes.map(d => Object.create(d));

      //  Give d3 force properties to the nodes
      const simulation = d3
        .forceSimulation(nodes)
        .force("link", d3.forceLink(links).id(d => d.id))
        .force("charge", d3.forceManyBody())
        .force("center", d3.forceCenter(500 / 2, 500 / 2));

      const svg = d3.select("#example-mesh");

      //  Draw the links
      const link = svg
        .append("g")
        .attr("stroke", "#999")
        .attr("stroke-opacity", 0.6)
        .selectAll("line")
        .data(links)
        .join("line")
        .attr("stroke-width", d => Math.sqrt(d.value));

      //  Draw the nodes with the force effect
      const node = svg
        .append("g")
        .attr("stroke", "#fff")
        .attr("stroke-width", 1.5)
        .selectAll("circle")
        .data(nodes)
        .join("circle")
        .attr("r", 5)
        .attr("fill", "red")
        .call(drag(simulation));

      node.append("title").text(d => d.id);

      //  Update the nodes positions depending on the simulation properties
      simulation.on("tick", () => {
        link
          .attr("x1", d => d.source.x)
          .attr("y1", d => d.source.y)
          .attr("x2", d => d.target.x)
          .attr("y2", d => d.target.y);

        node.attr("cx", d => d.x).attr("cy", d => d.y);
      });
    });
  }
};
</script>


<style scoped>
</style>


