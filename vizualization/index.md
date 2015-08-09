---
layout: default
title: "Vizualization Programming Assignment 2"
---

<style>
.node {
  stroke: #fff;
  stroke-width: 1.5px;
}
.link {
  stroke: #999;
  stroke-opacity: .6;
}
</style>

<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.5/d3.min.js"></script>
<script>

var width = 1000,
    height = 500,
    radius = 10;

var color = d3.scale.category20();

var force = d3.layout.force()
    .charge(-50)
    .linkDistance(5)
    .size([width, height]);

var svg = d3.select("body").append("svg")
    .attr("width", width)
    .attr("height", height);

d3.json("graph.json", function(error, graph) {
  if (error) throw error;

  force
      .nodes(graph.nodes)
      .links(graph.links)
      .start();

  var link = svg.selectAll(".link")
      .data(graph.links)
    .enter().append("line")
      .attr("class", "link")
      .style("stroke-width", function(d) { return Math.sqrt(d.value)/2; });

  var node = svg.selectAll(".node")
      .data(graph.nodes)
    .enter().append("circle")
      .attr("class", "node")
      .attr("r", function(d) { return Math.sqrt(d.value)/5; })
      .style("fill", function(d) { return color(d.group); })
      .call(force.drag);

  node.append("title")
      .text(function(d) { return d.name; });

  force.on("tick", function() {
    node.attr("cx", function(d) { return d.x = Math.max(radius, Math.min(width - radius, d.x)); })
        .attr("cy", function(d) { return d.y = Math.max(radius, Math.min(height - radius, d.y)); });

    link.attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });
  });
});

</script>

The data displayed on this is based on <a href="http://www.cs.mcgill.ca/~rwest/wikispeedia/">Wikispeedia</a> (with data retrieved from <a href="http://snap.stanford.edu/data/wikispeedia.html">SNAP</a>), a game in which you are given two Wikipedia articles, and starting from the first article your goal is to navigate to the second one exclusively by following links.
Each node is one the 200 most visited pages, and each edge is the most visited link between these two pages.
Node and edge sizes are based on the number of visits and clicks respectively,
and node color is based on article category.
You can hover on a node to know the page name and its category.

From this data, we observe that geographic pages (in dark blue) are the most visited and the most used to navigate between different subjects, while scientific pages (in light green) are used mostly to go to other scientific pages.
Also visible in this graph is the fact that the english version of wikipedia is used, addressing an english speaking population with research articulated around United States, England, United Kingdom and Europe to cite a few.
