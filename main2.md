<!DOCTYPE html>
<html>
<head>
  <title>Shortest Path Visualization</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    #canvas {
      border: 1px solid #000000;
      background-image: url('SDmap.png');
      background-size: contain;
      background-repeat: no-repeat;
      background-position: center;
    }
  </style>
</head>
<body>
  <h1>Shortest Path Visualization</h1>
  <canvas id="canvas" width="1072" height="829"></canvas>
 <script>
 // Vertex class to represent each HTML element
 class Vertex {
 constructor(id, x, y) {
 this.id = id; // id of the vertex
 this.x = x; // x-coordinate of the vertex
 this.y = y; // y-coordinate of the vertex
 this.adjacent = []; // array to store adjacent vertices
 }
 // Function to add an adjacent vertex
 addAdjacent(vertex) {
 this.adjacent.push(vertex);
 }
 }
 // Graph class to hold all the vertices
 class Graph {
 constructor() {
 this.vertices = []; // array to store all vertices
 this.map = {}; // hash map to store vertices by their ids
 }
 // Function to add a vertex to the graph
 addVertex(vertex) {
 this.vertices.push(vertex);
 this.map[vertex.id] = vertex; // add vertex to the map
 }
 }
 // Function to calculate the Euclidean distance between two vertices
 function getDistance(v1, v2) {
 const dx = v1.x - v2.x;
 const dy = v1.y - v2.y;
 return Math.sqrt(dx * dx + dy * dy);
 }
 // Dijkstra's algorithm implementation
 function dijkstra(graph, startId) {
 const distances = {}; // object to store distances from start vertex to all other vertices
 const previous = {}; // object to store previous vertex in the shortest path
 const unvisited = new Set(); // set to store unvisited vertices
 // Initialize distances and previous objects
 graph.vertices.forEach((vertex) => {
 distances[vertex.id] = Infinity;
 previous[vertex.id] = null;
 unvisited.add(vertex.id);
 });
 distances[startId] = 0; // distance to start vertex is 0
 while (unvisited.size > 0) {
 let minId = null;
 // Find the unvisited vertex with the smallest distance
 unvisited.forEach((vertexId) => {
 if (minId === null || distances[vertexId] < distances[minId]) {
 minId = vertexId;
 }
 });
 unvisited.delete(minId); // remove the vertex from the unvisited set
 const current = graph.map[minId]; // use the map to access the vertex in constant time
 // Update distances and previous for each adjacent vertex
 current.adjacent.forEach((neighbor) => {
 const alt = distances[minId] + getDistance(current, neighbor);
 if (alt < distances[neighbor.id]) {
 distances[neighbor.id] = alt;
 previous[neighbor.id] = current.id;
 }
 });
 }
 return previous; // return the previous object
 }
 // Function to draw the shortest path on the canvas
 function drawShortestPath(graph, previous) {
 const canvas = document.getElementById("canvas");
 const ctx = canvas.getContext("2d");
 ctx.clearRect(0, 0, canvas.width, canvas.height); // clear the canvas
 // Draw all vertices as white circles
 graph.vertices.forEach((vertex) => {
 ctx.beginPath();
 ctx.arc(vertex.x, vertex.y, 10, 0, 2 * Math.PI);
 ctx.fillStyle = "#000000";
 ctx.fill();
 ctx.closePath();
 });
 // Draw the shortest path as a red line
 ctx.beginPath();
 ctx.strokeStyle = "#FF0000";
 ctx.lineWidth = 3;
 graph.vertices.forEach((vertex) => {
 let currentId = vertex.id;
 let nextId = previous[currentId];
 while (nextId !== null) {
 const current = graph.map[currentId]; // use the map to access the vertex in constant time
 const next = graph.map[nextId];
 ctx.moveTo(current.x, current.y);
 ctx.lineTo(next.x, next.y);
 currentId = nextId;
 nextId = previous[nextId];
 }
 });
 ctx.stroke();
 ctx.closePath();
 }
 // Function to handle the mouse down event
 function handleMouseDown(e) {
 const canvas = e.target;
 const rect = canvas.getBoundingClientRect();
 const mouseX = e.clientX - rect.left;
 const mouseY = e.clientY - rect.top;
 // Find the vertex that the user clicked on (if any)
 const vertex = graph.vertices.find((vertex) => {
 const dx = vertex.x - mouseX;
 const dy = vertex.y - mouseY;
 return dx * dx + dy * dy <= 100; // check if the click is within the vertex's radius
 });
 if (vertex) {
 // Store the selected vertex and the starting position of the line
 selectedVertex = vertex;
 lineStartX = vertex.x;
 lineStartY = vertex.y;
 // Add mouse move and mouse up event listeners
 canvas.addEventListener("mousemove", handleMouseMove);
 canvas.addEventListener("mouseup", handleMouseUp);
 }
 }
 // Function to handle the mouse move event
 function handleMouseMove(e) {
 const canvas = e.target;
 const rect = canvas.getBoundingClientRect();
 const mouseX = e.clientX - rect.left;
 const mouseY = e.clientY - rect.top;
 // Update the line end position
 lineEndX = mouseX;
 lineEndY = mouseY;
 // Redraw the canvas
 drawShortestPath(graph, previous);
 // Draw the temporary line from the selected vertex to the mouse position
 const ctx = canvas.getContext("2d");
 ctx.beginPath();
 ctx.strokeStyle = "#0000FF";
 ctx.lineWidth = 2;
 ctx.moveTo(lineStartX, lineStartY);
 ctx.lineTo(lineEndX, lineEndY);
 ctx.stroke();
 ctx.closePath();
 }
 // Function to handle the mouse up event
 function handleMouseUp(e) {
 const canvas = e.target;
 // Find the vertex that the user released the mouse on (if any)
 const vertex = graph.vertices.find((vertex) => {
 const dx = vertex.x - lineEndX;
 const dy = vertex.y - lineEndY;
 return dx * dx + dy * dy <= 100; // check if the release point is within the vertex's radius
 });
 if (vertex) {
 // Add an adjacency relationship between the selected vertex and the released vertex
 selectedVertex.addAdjacent(vertex);
 vertex.addAdjacent(selectedVertex);
 // Redraw the canvas with the updated graph
 drawShortestPath(graph, previous);
 }
 // Remove the event listeners
 canvas.removeEventListener("mousemove", handleMouseMove);
 canvas.removeEventListener("mouseup", handleMouseUp);
 }
 // Example usage
 const graph = new Graph();
 // Define HTML elements and their positions
 const elementA = new Vertex("A", 69, 69);
 const elementB = new Vertex("B", 11, 22);
 const elementC = new Vertex("C", 420, 420);
 const elementD = new Vertex("D", 34, 22);
 const elementE = new Vertex("E", 90, 56);
 // Define adjacency relationships
 elementA.addAdjacent(elementB);
 elementA.addAdjacent(elementD);
 elementB.addAdjacent(elementC);
 elementC.addAdjacent(elementD);
 elementC.addAdjacent(elementE);
 elementD.addAdjacent(elementE);
 // Add vertices to the graph
 graph.addVertex(elementA);
 graph.addVertex(elementB);
 graph.addVertex(elementC);
 graph.addVertex(elementD);
 graph.addVertex(elementE);
 // Find the shortest path from elementA to all other elements
 const previous = dijkstra(graph, "A");
 // Draw the shortest path on the canvas
 drawShortestPath(graph, previous);
 // Calculate the pixel length of the shortest path
 let pixelLength = 0;
 graph.vertices.forEach((vertex) => {
 let currentId = vertex.id;
 let nextId = previous[currentId];
 while (nextId !== null) {
 const current = graph.map[currentId]; // use the map to access the vertex in constant time
 const next = graph.map[nextId];
 pixelLength += getDistance(current, next);
 currentId = nextId;
 nextId = previous[nextId];
 }
 });
 // Store the pixel length in a global variable called path_length
 const path_length = Math.round(pixelLength);
 // Log the pixel length to the console
 console.log("Pixel length of shortest path:", path_length);
 // Initialize variables for handling dragging and dropping lines
 let selectedVertex = null;
 let lineStartX = 0;
 let lineStartY = 0;
 let lineEndX = 0;
 let lineEndY = 0;
 // Add a mouse down event listener to the canvas
 const canvas = document.getElementById("canvas");
 canvas.addEventListener("mousedown", handleMouseDown);

 </script>
</body>
</html>
