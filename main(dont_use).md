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
    <button onclick="post_name()">Restart Game</button>
    </div>
  <div id="playing">
    <p>Press Reset Lines twice to fully reset the map</p>
    <button id="calculateButton">Calculate Total Distance</button>
    <button id="resetButton">Reset Lines</button>
    <p>Total Distance: <span id="totalDistance">-</span></p>
  </div>
</body>

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

let lastMousePos = [];
let currentMousePos = [];
let lines = [];
let calculateButton;
let resetButton;
let totalDistanceElement;
let backgroundImage;
let gameState;
var missionBay, OceanFW, belmontPark, seaworld, fashionV, oceanBeach, BazaarDM, whaleyHouse, zoo, elPrado, balboaPark, fleetSciCenter, aerospaceMuseum, museumofPhotographicArts;
let firehouseMuseum, maritimeMuseum, shorelinePark, ussMidway, gaslampQuarter, seaportVillage, cBridge, cBeach, cIsland, cabrilloMonu, oldPntLomaLighthouse, hotelDelC;
let a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z;

gameState = 1;

const finish_screen = document.getElementById("finish");
const reset_screen = document.getElementById("reset");
const playing_screen = document.getElementById("playing");

finish_screen.style.display = "none";
reset_screen.style.display = "none";

function preload() {
  backgroundImage = loadImage('SDmap.png');
  a = loadImage('letterA.png');

}

function setup() {
  createCanvas(1072,829);

  missionBay = createSprite(150,200,20,20);
  missionBay.addImage(a);
  OceanFW = createSprite(90,200,20,20);
  belmontPark = createSprite(95,220,20,20);
  seaworld = createSprite(165,230,20,20);
  fashionV = createSprite(316,225,20,20);
  oceanBeach = createSprite(100,276,20,20);
  BazaarDM = createSprite(150,200,20,20);
  whaleyHouse = createSprite(150,200,20,20);
  zoo = createSprite(150,200,20,20);
  elPrado = createSprite(150,200,20,20);
  balboaPark = createSprite(150,200,20,20);
  fleetSciCenter = createSprite(150,200,20,20);
  aerospaceMuseum = createSprite(150,200,20,20);
  museumofPhotographicArts = createSprite(150,200,20,20);

  let elementA = new Vertex("A", 69, 69);
  let elementB = new Vertex("B", 11, 22);
  let elementC = new Vertex("C", 420, 420);
  let elementD = new Vertex("D", 34, 22);
  let elementE = new Vertex("E", 90, 56);

  calculateButton = select('#calculateButton');
  calculateButton.mousePressed(calculateTotalDistance);

  resetButton = select('#resetButton');
  resetButton.mousePressed(resetLines);

  totalDistanceElement = select('#totalDistance');

  
}

function draw() {
  background(backgroundImage);

  drawSprites();

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