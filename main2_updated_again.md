<!DOCTYPE html>
<html>
<head>
  <style>
    #canvas {
      border: 1px solid black;
    }
  </style>
</head>
<body>
  <canvas id="canvas" width="500" height="500"></canvas>

  <script>
    // Vertex class
    class Vertex {
      constructor(id, x, y) {
        this.id = id;
        this.x = x;
        this.y = y;
        this.adjacentVertices = [];
        this.connected = false;
      }

      addAdjacent(vertex) {
        this.adjacentVertices.push(vertex);
      }
    }

    // Graph class
    class Graph {
      constructor() {
        this.vertices = [];
      }

      addVertex(vertex) {
        this.vertices.push(vertex);
      }

      checkAllVerticesConnected() {
        for (const vertex of this.vertices) {
          if (!vertex.connected) {
            return false;
          }
        }
        return true;
      }
    }

    // Dijkstra's algorithm
    function dijkstra(graph, startId) {
      const distances = {};
      const previous = {};
      const queue = [];

      // Initialize distances and previous vertices
      graph.vertices.forEach((vertex) => {
        distances[vertex.id] = Infinity;
        previous[vertex.id] = null;
        queue.push(vertex);
      });

      // Set the distance of the start vertex to 0
      distances[startId] = 0;

      while (queue.length > 0) {
        // Find the vertex with the minimum distance
        let minDistance = Infinity;
        let minVertex = null;
        queue.forEach((vertex) => {
          if (distances[vertex.id] < minDistance) {
            minDistance = distances[vertex.id];
            minVertex = vertex;
          }
        });

        // Remove the vertex from the queue
        queue.splice(queue.indexOf(minVertex), 1);

        minVertex.adjacentVertices.forEach((adjVertex) => {
          // Calculate the new distance
          const distance = minDistance + calculateDistance(minVertex, adjVertex);
          if (distance < distances[adjVertex.id]) {
            // Update the distance and previous vertex
            distances[adjVertex.id] = distance;
            previous[adjVertex.id] = minVertex.id;
          }
        });
      }

      return previous;
    }

    // Function to calculate the Euclidean distance between two vertices
    function calculateDistance(vertex1, vertex2) {
      const dx = vertex2.x - vertex1.x;
      const dy = vertex2.y - vertex1.y;
      return Math.sqrt(dx * dx + dy * dy);
    }

    // Function to draw the graph on the canvas
    function drawGraph(graph) {
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw vertices
      graph.vertices.forEach((vertex) => {
        ctx.beginPath();
        ctx.arc(vertex.x, vertex.y, 10, 0, 2 * Math.PI);
        ctx.fillStyle = vertex.connected ? "#00FF00" : "#000000";
        ctx.fill();
        ctx.closePath();
      });

      // Draw edges
      graph.vertices.forEach((vertex) => {
        vertex.adjacentVertices.forEach((adjVertex) => {
          ctx.beginPath();
          ctx.moveTo(vertex.x, vertex.y);
          ctx.lineTo(adjVertex.x, adjVertex.y);
          ctx.strokeStyle = "#000000";
          ctx.lineWidth = 2;
          ctx.stroke();
          ctx.closePath();
        });
      });
    }

    // Function to handle the mouse down event
    function handleMouseDown(e) {
      const canvas = e.target;
      const rect = canvas.getBoundingClientRect();
      const mouseX = e.clientX - rect.left;
      const mouseY = e.clientY - rect.top;

      // Check if the mouse down position is within 10 pixels of a vertex
      const clickedVertex = graph.vertices.find((vertex) => {
        const distance = calculateDistance(vertex, { x: mouseX, y: mouseY });
        return distance <= 10;
      });

      if (clickedVertex) {
        // Store the selected vertex and add event listeners for mouse move and mouse up events
        selectedVertex = clickedVertex;
        lineStartX = clickedVertex.x;
        lineStartY = clickedVertex.y;
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

      // Update the end position of the line and redraw the canvas
      lineEndX = mouseX;
      lineEndY = mouseY;
      drawGraph(graph);

      // Draw a temporary line from the selected vertex to the current mouse position
      const ctx = canvas.getContext("2d");
      ctx.beginPath();
      ctx.moveTo(lineStartX, lineStartY);
      ctx.lineTo(lineEndX, lineEndY);
      ctx.strokeStyle = "#000000";
      ctx.lineWidth = 2;
      ctx.stroke();
      ctx.closePath();
    }

    // Function to handle the mouse up event
    function handleMouseUp(e) {
      const canvas = e.target;
      const rect = canvas.getBoundingClientRect();
      const mouseX = e.clientX - rect.left;
      const mouseY = e.clientY - rect.top;

      // Find the vertex that is within 10 pixels of the mouse release
      const snappedVertex = graph.vertices.find((vertex) => {
        const distance = calculateDistance(vertex, { x: mouseX, y: mouseY });
        return distance <= 10;
      });

      if (snappedVertex) {
        // Connect the line to the snapped vertex by adding them as adjacent vertices
        selectedVertex.addAdjacent(snappedVertex);
        snappedVertex.addAdjacent(selectedVertex);
        selectedVertex.connected = true;
        snappedVertex.connected = true;
      }

      // Redraw the canvas with the updated graph and remove the mouse move and mouse up event listeners
      drawGraph(graph);
      canvas.removeEventListener("mousemove", handleMouseMove);
      canvas.removeEventListener("mouseup", handleMouseUp);

      // Check if all vertices are connected
      const allVerticesConnected = graph.checkAllVerticesConnected();
      console.log("All vertices connected:", allVerticesConnected);
    }

    // Create a graph and add vertices
    const graph = new Graph();
    const vertexA = new Vertex("A", 100, 100);
    const vertexB = new Vertex("B", 200, 200);
    const vertexC = new Vertex("C", 300, 100);
    graph.addVertex(vertexA);
    graph.addVertex(vertexB);
    graph.addVertex(vertexC);

    // Initialize variables for line drawing
    let selectedVertex = null;
    let lineStartX = null;
    let lineStartY = null;
    let lineEndX = null;
    let lineEndY = null;

    // Add mouse down event listener to the canvas
    const canvas = document.getElementById("canvas");
    canvas.addEventListener("mousedown", handleMouseDown);

    // Draw the initial graph on the canvas
    drawGraph(graph);
  </script>
</body>
</html>
