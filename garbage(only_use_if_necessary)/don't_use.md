<h1>Welcome to the San Diego Map Game!</h1>

<script>

var mapIMG, map;
var Xlist = [];
var Ylist = [];
var line;

function preload(){
  
  mapIMG = loadImage("SD_Map.png");
  
}



function setup() {
  createCanvas(windowWidth,windowHeight);
  map = createSprite(500,500,1000,1000);
  // map.addImage(mapIMG);
}

function draw() {
  background("yellow");
  if(mousePressedOver(map)){
    append(Xlist, mouseX);
    append(Ylist, mouseY);
    if(Xlist.length === 2){
      line = line(Xlist[0], Ylist[0], Xlist[1], Xlist[1]);
      Xlist = [];
      Ylist = [];
    }
    console.log(Xlist);
    console.log(Ylist);
  }
  
  drawSprites();
}

function mousePressed(){
  // createLine();
}

// let nodes = [];
// let startNode = null;
// let endNode = null;
// let drawing = false;

// function setup() {
//   createCanvas(400, 400);
// }

// function draw() {
//   background(220);
//   drawNodes();
//   drawEdges();
// }

// function drawNodes() {
//   for (let node of nodes) {
//     fill(255);
//     stroke(0);
//     ellipse(node.x, node.y, 20, 20);
//   }
// }

// function drawEdges() {
//   for (let node of nodes) {
//     for (let neighbor of node.neighbors) {
//       stroke(0);
//       line(node.x, node.y, neighbor.x, neighbor.y);
//     }
//   }
// }

// function mousePressed() {
//   for (let node of nodes) {
//     if (dist(mouseX, mouseY, node.x, node.y) < 10) {
//       if (!startNode) {
//         startNode = node;
//         startNode.color = color(0, 255, 0);
//       } else if (!endNode && node !== startNode) {
//         endNode = node;
//         endNode.color = color(255, 0, 0);
//         drawing = true;
//       }
//       return;
//     }
//   }

//   if (!drawing) {
//     let newNode = createNode(mouseX, mouseY);
//     nodes.push(newNode);
//   }
// }

// function mouseDragged() {
//   if (drawing) {
//     let newNode = createNode(mouseX, mouseY);
//     nodes.push(newNode);
//   }
// }

// function mouseReleased() {
//   drawing = false;
// }

// function createNode(x, y) {
//   let node = {
//     x: x,
//     y: y,
//     neighbors: [],
//     color: color(255)
//   };
//   return node;
// }
</script>