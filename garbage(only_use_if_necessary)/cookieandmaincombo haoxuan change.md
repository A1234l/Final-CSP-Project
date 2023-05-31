<html>
<head>
  <h1 id="title-thing">San Diego Map Game</h1>
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
    #finish-form{
      text-align: center;
    }
  </style>
  <div id="start-page">
    <div class="button-container">
    <button class="gen-button" onclick="gameScreen(1)" id="start-button">Start Game</button>
    </div>
  </div>
  <div id="end-page">
    <h1>Thank you for playing!</h1>
    <p>If you would like to play another round, please click the button below.</p>
    <div class="button-container">
      <button class="gen-button" onclick="gameScreen(3)">Return to Game Page</button>
    </div>
    <h3>If you would like to return to the home page, please click <span id="linked-gametohome"><a href="{{site.baseurl}}/index">here</a></span>.</h3>
  </div>
</head>
<body>
    <div id="finish-form">
      <h3 style="color:white;">Game over, please record your score!</h3>
      <p style="color:white;">The shortest route is shown on the map with the (enter color) lines</p>
      <form action="javascript:userCreate()">
        <p><label>
            Username:
            <input type="text" name="username" id="username" placeholder="Enter username here" required>
        </label></p>
        <p><label>
            Total Distance of your route: <span id="totalDistance">0.00</span> miles
        </label></p>
        <p><label>
            Total Distance of shortest route: <span id="totalDistanceClosest">0.00</span> miles
        </label></p>
        <p><label>
            Calculated score: <span id="scoring">0</span>t
        </label></p>
        <p><label>
            Locations visited: <span id="locationList">NA</span>
        </label></p>
        <p>
        <!-- Popup message on button click -->
            <button onclick="alert('Your score has been posted!')" id="form-submit-button">Submit</button>
        </p>
      </form>
      <!-- Temporary button, remove later -->
      <div class="button-container">
        <button id="temporary" onclick="userCreate()" class="gen-button">Temporary</button>
      </div>
    </div>
  <div id="game-page">
    <div class="button-container">
      <button id="game-finish-button" class="gen-button" onclick="gameScreen(2)">Finish Game</button>
      <button id="resetButton" class="gen-button">Reset</button>
    </div>
    <canvas id="canvas" width="1072" height="829"></canvas>
  </div>
  <script>
    const startPage = document.getElementById("start-page");
    const endPage = document.getElementById("end-page");
    const gamePage = document.getElementById("game-page");
    const finishButton = document.getElementById("game-finish-button");
    const resetButton = document.getElementById("resetButton");
    const canvas = document.getElementById("canvas");
    const finishForm = document.getElementById("finish-form");
    const submitButton = document.getElementById("form-submit-button");
    const temp = document.getElementById("temporary");
    const totalDistanceDisplay = document.getElementById("totalDistance");
    const scoreDisplay = document.getElementById("scoring");
    // Initially hides end page and game page and finish button
    endPage.style.display = "none";
    gamePage.style.display = "none";
    finishButton.style.display = "none";
    finishForm.style.display = "none";
    // Function switches screen based on status parameter
    function gameScreen(status){
      if(status === 1){
        startPage.style.display = "none";
        gamePage.style.display = "block";
        resetButton.style.display = "block";
        finishButton.style.display = "none";
      }
      if(status === 2){
        finishForm.style.display = "block";
        resetButton.style.display = "none";
        finishButton.style.display = "none";
        // Draw the shortest path on the canvas
        drawShortestPath(heuristic, shortestPath);
        var intShortestDistance=parseInt(shortestDistanceResult.innerHTML);
        var intUserDistance=parseInt(totalDistanceDisplay.innerHTML);
        var score = 1.0*Math.pow(1000,(-(Math.log(intUserDistance/intShortestDistance)-1)));
        if(intShortestDistance>intUserDistance){
            totalDistanceDisplay.textContent = "**error**";
            scoreDisplay.textContent = "NA";
        }
        scoreDisplay.textContent = score.toString();
      }
      if(status === 3){
        startPage.style.display = "block";
        endPage.style.display = "none";
      }
    }
    // Add POST here for api
    function userCreate(){
      finishForm.style.display = "none";
      gamePage.style.display = "none";
      endPage.style.display = "block";
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
        const visited = new Set(); // Set to store visited vertices
        const stack = []; // Stack for DFS traversal

        // Start DFS from the first vertex in the graph
        stack.push(graph.vertices[0]);

        while (stack.length > 0) {
          const vertex = stack.pop();
          visited.add(vertex);

          // Add all adjacent unvisited vertices to the stack
          for (const adjacentVertex of vertex.adjacent) {
            if (!visited.has(adjacentVertex)) {
              stack.push(adjacentVertex);
            }
          }
        }

      // Check if all vertices are visited
      return visited.size === graph.vertices.length;
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

// Function to generate all possible paths that visit all vertices exactly once
 function generatePaths(graph) {
 const paths = [];
 const visited = new Set();
 
    function dfs(path) {
    if (path.length === graph.vertices.length) {
    paths.push(path);
    return;
    }

    graph.vertices.forEach((vertex) => {
    if (!visited.has(vertex)) {
    visited.add(vertex);
    dfs([...path, vertex]);
    visited.delete(vertex);
    }
    });
    }

    graph.vertices.forEach((vertex) => {
    visited.add(vertex);
    dfs([vertex]);
    visited.delete(vertex);
    });

    return paths;
    }

    // Dijkstra's algorithm implementation
    function dijkstra(graph, start, end) {
    const distances = {}; // object to store distances from start vertex to all other vertices
    const previous = {}; // object to store previous vertex in the shortest path
    const unvisited = new Set(); // set to store unvisited vertices

    // Initialize distances and previous objects
    graph.vertices.forEach((vertex) => {
    distances[vertex.id] = Infinity;
    previous[vertex.id] = null;
    unvisited.add(vertex.id);
    });

    distances[start.id] = 0; // distance to start vertex is 0

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

    if (current === end) {
    break;
    }

    // Update distances and previous for each adjacent vertex
    current.adjacent.forEach((neighbor) => {
    const alt = distances[minId] + heuristic.calculateDistance(current, neighbor);

    if (alt < distances[neighbor.id]) {
    distances[neighbor.id] = alt;
    previous[neighbor.id] = current.id;
    }
    });
    }

    const path = [];
    let current = end;
    while (current !== start) {
    path.unshift(current);
    current = graph.map[previous[current.id]];
    }
    path.unshift(start);

    return path; // return the shortest path
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
              // allows user to finish if all points connected
              if(allVerticesConnected === true){
                finishButton.style.display = "block";
              }
              // Calculate and update the total distance
              const totalDistance = graph.calculateTotalDistance();
              totalDistanceDisplay.textContent = (totalDistance/54).toFixed(2);
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
            // hides finish button if lines are reset
            finishButton.style.display = "none";
          }
          // Create the graph
          const graph = new Graph();
          // Define the vertices as an array of objects
          let vertices = [
            { id: "A", x: 150, y: 200 },
            { id: "B", x: 90, y: 200 },
            { id: "C", x: 95, y: 220 },
            { id: "D", x: 165, y: 230 },
            { id: "E", x: 316, y: 225 },
            { id: "F", x: 100, y: 276 },
            // Add more vertices here as needed
          ];
          // Loop through the vertices array and create a new Vertex object for each one
          for (const vertex of vertices) {
            const newVertex = new Vertex(vertex.id, vertex.x, vertex.y);
            graph.addVertex(newVertex);
          }

        // Example usage
        const heuristic = new Graph();
        // Loop through the vertices array and create a new Vertex object for each one
        for (const vertex of vertices) {
            const newVertex = new Vertex(vertex.id, vertex.x, vertex.y);
            heuristic.addVertex(newVertex);
        }
        // Function to calculate the total distance of heuristic path
        function getPathDistance(path) {
        let distance = 0;
        for (let i = 0; i < path.length - 1; i++) {
        distance += heuristic.calculateDistance(path[i], path[i+1]);
        }
        return distance;
        }

        // Function to draw the shortest path on the canvas
        function drawShortestPath(graph, path) {
        const canvas = document.getElementById("canvas");
        const ctx = canvas.getContext("2d");

        ctx.clearRect(0, 0, canvas.width, canvas.height); // clear the canvas

        // Draw all vertices as white circles
        graph.vertices.forEach((vertex) => {
        ctx.beginPath();
        ctx.arc(vertex.x, vertex.y, 10, 0, 2 * Math.PI);
        ctx.fillStyle = "#FFFFFF";
        ctx.fill();
        ctx.closePath();

        });

        // Draw the path as a red line
        ctx.beginPath();
        ctx.strokeStyle = "#FF0000";
        ctx.lineWidth = 3;

        for (let i = 0; i < path.length - 1; i++) {
        const current = path[i];
        const next = path[i+1];
        ctx.moveTo(current.x, current.y);
        ctx.lineTo(next.x, next.y);
        }

        ctx.stroke();
        ctx.closePath();
        }

        // Define adjacency relationships
        heuristic.vertices.forEach((vertex) => {
        const closestPoints = vertices
            .filter((p) => p.id !== vertex.id)
            .sort((a, b) => heuristic.calculateDistance(vertex, a) - heuristic.calculateDistance(vertex, b))
            .slice(0, 2);

        closestPoints.forEach((point) => {
            const adjacentVertex = heuristic.map[point.id];
            vertex.addAdjacent(adjacentVertex);
        });
        });

        // Generate all possible paths and find the shortest one
        const paths = generatePaths(heuristic);
        let shortestPath = null;
        let shortestDistance = Infinity;
        paths.forEach((path) => {
        const distance = getPathDistance(path);
        if (distance < shortestDistance) {
        shortestPath = path;
        shortestDistance = distance;
        }
        });

         // Store the pixel length in a global variable called path_length
        const path_length = shortestDistance;

        // Log the pixel length to the console
        console.log("Pixel length of shortest path:", path_length);

        const shortestDistanceResult = document.getElementById("totalDistanceClosest");
        shortestDistanceResult.textContent = ((path_length*2)/54).toFixed(2);

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
          submitButton.addEventListener("click", handleResetButtonClick);
          temp.addEventListener("click", handleResetButtonClick);
        </script>
      </body>
</html>
<style>
  .banner{
    width: 100%;
    height: 200% !important;
    background-image: linear-gradient(rgba(0,0,0,0.75), rgba(0,0,0,0.75)),url(homepg.jpg);
    background-size: cover;
    background-position: center !important ;
  }
</style>