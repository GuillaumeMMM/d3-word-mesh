<template>
  <div>
    <h1 class="title">Word Mesh</h1>

    <!-- Define the svg -->
    <svg id="word-mesh" width="500px" height="500px"></svg>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  data() {
    return {
      nodes: [
          {name: 'couleur', id: 1},
          {name: 'orange', id: 2},
          {name: 'abricot', id: 3},
          {name: 'fruit', id: 4},
          {name: 'pulpe', id: 5},
          {name: 'jus', id: 6},
          {name: 'pépins', id: 7},
      ],
      links: [
          {source: 1, target: 2, value: 1},
          {source: 1, target: 3, value: 1},
          {source: 1, target: 4, value: 1},
          {source: 2, target: 3, value: 1},
          {source: 2, target: 4, value: 1},
          {source: 2, target: 5, value: 1},
          {source: 3, target: 4, value: 1},
          {source: 4, target: 5, value: 1},
          {source: 5, target: 6, value: 1},
          {source: 5, target: 7, value: 1},
      ]
    };
  },
  name: "WordMesh",
  mounted() {

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
      const links = this.links.map(d => Object.create(d));
      const nodes = this.nodes.map(d => Object.create(d));

      //  Give d3 force properties to the nodes
      const simulation = d3
        .forceSimulation(nodes)
        .force("link", d3.forceLink(links).id(d => d.id).distance(200).strength(1))
        .force("charge", d3.forceManyBody())
        .force("center", d3.forceCenter(500 / 2, 500 / 2));

      const svg = d3.select("#word-mesh");

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
        .attr("r", 15)
        .attr("stroke", 'none')
        .style('fill', 'none')
        .call(drag(simulation));

      const text = svg
        .append("g")
        .attr("stroke", "black")
        .selectAll("text")
        .data(nodes)
        .join("text")
        .text(d => d.name)
        .style('cursor', 'pointer')
        .attr('text-anchor', 'middle')
        .attr('alignment-baseline', 'middle')
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

        text.attr("x", d => d.x).attr("y", d => d.y);
      });
  }
};
</script>


<style scoped>
</style>


