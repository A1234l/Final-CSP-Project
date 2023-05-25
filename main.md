<html>
<head>
  <h1>Welcome to the San Diego Map Game!</h1>
  <style>
    #coding_body{
      font-family: Arial, sans-serif;
    }
    #playing{
      text-align: center;
    }
  </style>
</head>
<body id="coding_body">
  <div id="finish">
      <p>Game Over, please fill out the form below</p>
      <!-- Form does function create_user() -->
      <form action="javascript:post_name()">
          <p><label>
              User ID:
              <input type="text" name="username" id="username" placeholder="Enter username" required>
          </label></p>
          <p><label>
          <!-- Score has automatic input -->
              Score:
              <span name="score" id="score">0</span>
          </label></p>
          <p><label>
          <!-- Score has automatic input -->
              Distance:
              <span name="distance" id="distance">0</span>
          </label></p>
          <p><label>
          <!-- Score has automatic input -->
              Locations:
              <span name="location" id="location">location</span>
          </label></p>
          <p>
          <!-- Popup message on button click -->
              <button onclick="alert('Your entry has been posted!')">Submit</button>
          </p>
      </form>
  </div>
  <div id="reset">
    <p>Thank you for playing. If you would like to play another round, please click the button below.</p>
    <button onclick="post_name()">Restart Game<button>
  </div>
  <div id="playing">
    <p>Press <strong>Reset Lines</strong> twice to fully reset the map</p>
    <button id="calculateButton">Calculate Total Distance</button>
    <button id="resetButton">Reset Lines</button>
    <p>Total Distance: <span id="totalDistance">-</span></p>
  </div>
</body>

<script>
let lastMousePos = [];
let currentMousePos = [];
let lines = [];
let calculateButton;
let resetButton;
let totalDistanceElement;
let backgroundImage;
var gameState;

gameState = 1;

const finish_screen = document.getElementById("finish");
const reset_screen = document.getElementById("reset");
const playing_screen = document.getElementById("playing");

function preload() {
  backgroundImage = loadImage('SDmap.png');
}

function setup() {
  let canvas = createCanvas(1072,829);

  calculateButton = select('#calculateButton');
  calculateButton.mousePressed(calculateTotalDistance);

  resetButton = select('#resetButton');
  resetButton.mousePressed(resetLines);

  totalDistanceElement = select('#totalDistance');
}

function draw() {
  background(backgroundImage);

  for (let i = 0; i < lines.length; i++) {
    let linePoints = lines[i];
    let x1 = linePoints[0];
    let y1 = linePoints[1];
    let x2 = linePoints[2];
    let y2 = linePoints[3];

    stroke(255, 0, 0);
    strokeWeight(3);
    
    line(x1, y1, x2, y2);
  }
}

function mouseClicked() {
  if (mouseButton === LEFT) {
    if (lastMousePos.length === 0) {
      lastMousePos = [mouseX, mouseY];
    } else if (currentMousePos.length === 0) {
      currentMousePos = [mouseX, mouseY];
      let linePoints = lastMousePos.concat(currentMousePos);
      lines.push(linePoints);
      lastMousePos = [];
      currentMousePos = [];
    }
  }
}

function calculateTotalDistance() {
  let totalDistance = 0;
  for (let i = 0; i < lines.length; i++) {
    let linePoints = lines[i];
    let distance = calculateDistance(linePoints.slice(0, 2), linePoints.slice(2));
    totalDistance += distance;
  }
  totalDistanceElement.html(totalDistance);
}

function calculateDistance(point1, point2) {
  let dx = point2[0] - point1[0];
  let dy = point2[1] - point1[1];
  return Math.sqrt(dx * dx + dy * dy);
}

function resetLines() {
  lines = [];
  totalDistanceElement.html("-");
}
</script>
</html>