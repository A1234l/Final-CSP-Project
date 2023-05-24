<html>
<head>
  <h1>Welcome to the San Diego Map Game!</h1>
  <p>Press Reset twice to fully reset the map</p>
  <style>
    #coding_body{
      font-family: Arial, sans-serif;
    }
    #container_thingy{
      text-align: center;
    }
  </style>
</head>
<body id="coding_body">
  <div id="container_thingy">
    <button id="calculateButton">Calculate Total Distance</button>
    <button id="resetButton">Reset</button>
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