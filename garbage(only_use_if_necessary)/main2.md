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
     const distance = Math.sqrt(dx * dx + dy * dy);
     return distance <= 5; // check if the release point is within 5 pixels of the vertex
   });
   if (vertex) {
     // Connect the line to the snapped vertex
     selectedVertex.addAdjacent(vertex);
     vertex.addAdjacent(selectedVertex);
     // Disable further line dragging
     canvas.removeEventListener("mousemove", handleMouseMove);
     canvas.removeEventListener("mouseup", handleMouseUp);
   }
   // Redraw the canvas with the updated graph and line connection (if applicable)
   drawShortestPath(graph, previous);
 }
 // Create the graph
 const graph = new Graph();
 // Create vertices and add them to the graph
 const vertexA = new Vertex("A", 150, 100);
 const vertexB = new Vertex("B", 110, 200);
 const vertexC = new Vertex("C", 360, 500);
 const vertexD = new Vertex("D", 420, 420);
 graph.addVertex(vertexA);
 graph.addVertex(vertexB);
 graph.addVertex(vertexC);
 graph.addVertex(vertexD);
 // Connect the vertices
 vertexA.addAdjacent(vertexB);
 vertexA.addAdjacent(vertexC);
 vertexB.addAdjacent(vertexD);
 vertexC.addAdjacent(vertexD);
 // Initialize variables for line dragging
 let selectedVertex = null;
 let lineStartX = 0;
 let lineStartY = 0;
 let lineEndX = 0;
 let lineEndY = 0;
 // Calculate the shortest path
 const startId = "A";
 const previous = dijkstra(graph, startId);
 // Draw the shortest path on the canvas
 drawShortestPath(graph, previous);
 // Add mouse down event listener
 const canvas = document.getElementById("canvas");
 canvas.addEventListener("mousedown", handleMouseDown);
 </script>
</body>
</html>
