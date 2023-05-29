<html>
<head>
  <h1 id="title-thing">Welcome to the San Diego Map Game!</h1>
  <style>
    #title-thing{
      text-align: center;
      color: white;
    }
    .button-container{
      width: fit-content;
      margin: 0 auto;
    }
    .gen-button{
      display: block;
      width: 200px;
      padding: 15px 0;
      text-align: center;
      margin: 20px 10px;
      background: transparent;
      border-radius: 25px;
      border: 2px solid #009614;
      color: #fff;
      font-weight: bold;
      cursor: pointer;
      position: relative;
      overflow: hidden;
      transition: all 0.6s;
      color: white;
    }
    .gen-button:hover{
      background-color: #009614;
    }
    #canvas{
      border: 1px solid #000000;
      background-image: url('SDmap.png');
      background-position: center;
    }
    #linked-gametohome{
      background-color: yellow;
    }
    #end-page{
      text-align: center;
      color: white;
    }
    #game-page{
      text-align: center;
      color: white;
    }
  </style>
  <div class="button-container">
  <button class="gen-button" onclick="gameScreen(1)" id="start-button">Start Game</button>
  </div>
  <div id="end-page">
    <h1>Thank you for playing!</h1>
    <p>If you would like to play another round, please click the button below.</p>
    <button class="gen-button" onclick="gameScreen(3)">Return to Game Page</button>
    <h3>If you would like to return to the home page, please click <span id="linked-gametohome"><a href="{{site.baseurl}}/index">here</a></span>.</h3>
  </div>
</head>
<body>
  <div id="game-page">
    <div class="button-container">
      <button id="game-finish-button" class="gen-button" onclick="gameScreen(2)">Finish Game</button>
      <button id="resetButton" class="gen-button">Reset</button>
    </div>
    <p>Total Distance: <span id="totalDistance">0.00</span></p>
    <canvas id="canvas" width="1072" height="829"></canvas>
  </div>
  <script>
    const startButton = document.getElementById("start-button");
    const endPage = document.getElementById("end-page");
    const gamePage = document.getElementById("game-page");
    const finishButton = document.getElementById("game-finish-button");
    const resetButton = document.getElementById("resetButton");
    const canvas = document.getElementById("canvas");
    // Initially hides end page and game page and finish button
    endPage.style.display = "none";
    gamePage.style.display = "none";
    finishButton.style.display = "none";
    // Function switches screen based on status parameter
    function gameScreen(status){
      if(status === 1){
        startButton.style.display = "none";
        gamePage.style.display = "block";
      }
      if(status === 2){
        gamePage.style.display = "none";
        endPage.style.display = "block";
      }
      if(status === 3){
        startButton.style.display = "block";
        endPage.style.display = "none";
      }
    }
    // Vertex class to represent each HTML element
    class Vertex {
      constructor(id, x, y) {
        this.id = id; // id of the vertex
        this.x = x; // x-coordinate of the vertex
        this.y = y; // y-coordinate of the vertex
        this.adjacent = []; // array to store adjacent vertices
        this.connected = false; // flag to indicate if vertex is connected
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
      // Function to check if all vertices are connected
      checkAllVerticesConnected() {
        for (const vertex of this.vertices) {
          if (!vertex.connected) {
            return false;
          }
        }
        return true;
      }
      // Function to calculate the Euclidean distance between two vertices
      calculateDistance(v1, v2) {
        const dx = v1.x - v2.x;
        const dy = v1.y - v2.y;
        return Math.sqrt(dx * dx + dy * dy);
      }
      // Function to calculate the total distance of all lines
      calculateTotalDistance() {
        let totalDistance = 0;
        for (const vertex of this.vertices) {
          for (const adjacentVertex of vertex.adjacent) {
            totalDistance += this.calculateDistance(vertex, adjacentVertex);
          }
        }
        return totalDistance;
      }
    }
    // Function to draw the graph on the canvas
    function drawGraph(graph) {
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height); // clear the canvas
      // Draw all vertices as white circles
      graph.vertices.forEach((vertex) => {
        ctx.beginPath();
        ctx.arc(vertex.x, vertex.y, 10, 0, 2 * Math.PI);
        ctx.fillStyle = vertex.connected ? "#00FF00" : "#FFFFFF";
        ctx.fill();
        ctx.closePath();
      });
      // Draw the connected lines
      ctx.beginPath();
      ctx.strokeStyle = "#FF0000";
      ctx.lineWidth = 3;
      graph.vertices.forEach((vertex) => {
        vertex.adjacent.forEach((adjacentVertex) => {
          ctx.moveTo(vertex.x, vertex.y);
          ctx.lineTo(adjacentVertex.x, adjacentVertex.y);
        });
      });
      ctx.stroke();
      ctx.closePath();
    }
    // Function to handle the mouse down event
    function handleMouseDown(e) {
      if (allVerticesConnected) {
        return; // Return early if all vertices are already connected
      }
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
      drawGraph(graph);
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
        return distance <= 20; // check if the release point is within 20 pixels of the vertex
      });
      if (vertex && !vertex.connected) {
        // Connect the line to the snapped vertex
        selectedVertex.addAdjacent(vertex);
        vertex.addAdjacent(selectedVertex);
        // Set the vertices as connected
        selectedVertex.connected = true;
        vertex.connected = true;
        // Redraw the canvas with the updated graph and line connection
        drawGraph(graph);
        // Check if all vertices are connected
        allVerticesConnected = graph.checkAllVerticesConnected();
        console.log("All vertices connected:", allVerticesConnected);
        if(allVerticesConnected === true){
          finishButton.style.display = "block";
        }
        // Calculate and update the total distance
        const totalDistance = graph.calculateTotalDistance();
        document.getElementById("totalDistance").textContent = totalDistance.toFixed(2);
      }
      // Reset the line positions and remove the event listeners
      lineStartX = null;
      lineStartY = null;
      lineEndX = null;
      lineEndY = null;
      selectedVertex = null;
      canvas.removeEventListener("mousemove", handleMouseMove);
      canvas.removeEventListener("mouseup", handleMouseUp);
    }
    // Function to handle the reset button click event
    function handleResetButtonClick() {
      // Clear the canvas
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      // Reset all vertices
      for (const vertex of graph.vertices) {
        vertex.connected = false;
        vertex.adjacent = [];
      }
      // Reset the total distance
      document.getElementById("totalDistance").textContent = "0.00";
      // Redraw the empty canvas
      drawGraph(graph);
      // Reset the allVerticesConnected flag
      allVerticesConnected = graph.checkAllVerticesConnected();
    }
    // Create the graph
    const graph = new Graph();
    const v1 = new Vertex("A", 69, 69);
    const v2 = new Vertex("B", 222, 122);
    const v3 = new Vertex("C", 333, 125);
    const v4 = new Vertex("D", 235, 464);
    const v5 = new Vertex("E", 726, 123);
    const v6 = new Vertex("F", 46, 75);
    // Add vertices onto graph
    graph.addVertex(v1);
    graph.addVertex(v2);
    graph.addVertex(v3);
    graph.addVertex(v4);
    graph.addVertex(v5);
    graph.addVertex(v6);
    // Initialize variables
    let selectedVertex = null;
    let lineStartX = null;
    let lineStartY = null;
    let lineEndX = null;
    let lineEndY = null;
    let allVerticesConnected = graph.checkAllVerticesConnected();
    // Draw the initial graph
    drawGraph(graph);
    // Add event listeners
    canvas.addEventListener("mousedown", handleMouseDown);
    resetButton.addEventListener("click", handleResetButtonClick);
    finishButton.addEventListener("click", handleResetButtonClick);
  </script>
</body>
</html>
