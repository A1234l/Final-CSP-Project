<h1>Welcome to the San Diego Map Game!</h1>

<script>
let lastMousePos = [];
let currentMousePos = [];
let lines = [];

function setup() {
  createCanvas(400, 400);
}

function draw() {
  background(220);

  for (let i = 0; i < lines.length; i++) {
    let linePoints = lines[i];
    let x1 = linePoints[0];
    let y1 = linePoints[1];
    let x2 = linePoints[2];
    let y2 = linePoints[3];
    stroke(0);
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
</script>