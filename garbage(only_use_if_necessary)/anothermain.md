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
  <canvas id="canvas" width="800" height="600"></canvas>
  <script>
    class Vertex {
      constructor(id, x, y) {
        this.id = id;
        this.x = x;
        this.y = y;
        this.adjacent = [];
        this.connected = false;
      }
      addAdjacent(vertex) {
        this.adjacent.push(vertex);
      }
    }
    class Graph {
      constructor() {
        this.vertices = [];
      }
      addVertex(vertex) {
        this.vertices.push(vertex);
      }
      checkAllLinesAdjacent() {
        for (const vertex of this.vertices) {
          if (vertex.connected) {
            let adjacentConnected = false;
            for (const adjacent of vertex.adjacent) {
              if (adjacent.connected) {
                adjacentConnected = true;
                break;
              }
            }
            if (!adjacentConnected) {
              return false;
            }
          }
        }
        return true;
      }
    }
    function getDistance(v1, v2) {
      const dx = v1.x - v2.x;
      const dy = v1.y - v2.y;
      return Math.sqrt(dx * dx + dy * dy);
    }
    function dijkstra(graph, startId) {
      // Implementation of Dijkstra's algorithm
      // ...
    }
    function drawGraph(graph) {
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      graph.vertices.forEach((vertex) => {
        ctx.beginPath();
        ctx.arc(vertex.x, vertex.y, 10, 0, 2 * Math.PI);
        ctx.fillStyle = vertex.connected ? "#00FF00" : "#000000";
        ctx.fill();
        ctx.closePath();
      });
      ctx.beginPath();
      ctx.strokeStyle = "#FF0000";
      ctx.lineWidth = 3;
      graph.vertices.forEach((vertex) => {
        vertex.adjacent.forEach((adjacent) => {
          if (adjacent.connected && vertex.connected) {
            ctx.moveTo(vertex.x, vertex.y);
            ctx.lineTo(adjacent.x, adjacent.y);
          }
        });
      });
      ctx.stroke();
      ctx.closePath();
    }
    function handleMouseDown(e) {
      const canvas = e.target;
      const rect = canvas.getBoundingClientRect();
      const mouseX = e.clientX - rect.left;
      const mouseY = e.clientY - rect.top;
      const vertex = graph.vertices.find((vertex) => {
        const dx = vertex.x - mouseX;
        const dy = vertex.y - mouseY;
        return dx * dx + dy * dy <= 100;
      });
      if (vertex) {
        selectedVertex = vertex;
        lineStartX = vertex.x;
        lineStartY = vertex.y;
        canvas.addEventListener("mousemove", handleMouseMove);
        canvas.addEventListener("mouseup", handleMouseUp);
      }
    }
    function handleMouseMove(e) {
      const canvas = e.target;
      const rect = canvas.getBoundingClientRect();
      const mouseX = e.clientX - rect.left;
      const mouseY = e.clientY - rect.top;
      lineEndX = mouseX;
      lineEndY = mouseY;
      const ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawGraph(graph);
      ctx.beginPath();
      ctx.strokeStyle = "#0000FF";
      ctx.lineWidth = 2;
      ctx.moveTo(lineStartX, lineStartY);
      ctx.lineTo(lineEndX, lineEndY);
      ctx.stroke();
      ctx.closePath();
    }
    function handleMouseUp(e) {
      const canvas = e.target;
      const vertex = graph.vertices.find((vertex) => {
        const dx = vertex.x - lineEndX;
        const dy = vertex.y - lineEndY;
        return dx * dx + dy * dy <= 100;
      });
      if (vertex) {
        if (!selectedVertex.connected && !vertex.connected) {
          selectedVertex.addAdjacent(vertex);
          vertex.addAdjacent(selectedVertex);
          selectedVertex.connected = true;
          vertex.connected = true;
          if (graph.checkAllLinesAdjacent()) {
            console.log(true);
          }
        }
      }
      const ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawGraph(graph);
      lineStartX = null;
      lineStartY = null;
      lineEndX = null;
      lineEndY = null;
      selectedVertex = null;
      canvas.removeEventListener("mousemove", handleMouseMove);
      canvas.removeEventListener("mouseup", handleMouseUp);
    }
    const graph = new Graph();
    const v1 = new Vertex("A", 100, 100);
    const v2 = new Vertex("B", 200, 200);
    const v3 = new Vertex("C", 300, 100);
    const v4 = new Vertex("D", 400, 200);
    const v5 = new Vertex("E", 500, 100);
    const v6 = new Vertex("F", 600, 200);
    graph.addVertex(v1);
    graph.addVertex(v2);
    graph.addVertex(v3);
    graph.addVertex(v4);
    graph.addVertex(v5);
    graph.addVertex(v6);
    v1.addAdjacent(v2);
    v2.addAdjacent(v3);
    v3.addAdjacent(v4);
    v4.addAdjacent(v5);
    v5.addAdjacent(v6);
    const canvas = document.getElementById("canvas");
    canvas.addEventListener("mousedown", handleMouseDown);
    drawGraph(graph);
  </script>
</body>
</html>
