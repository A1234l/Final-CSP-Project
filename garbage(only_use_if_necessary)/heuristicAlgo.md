<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Shortest Path Finder</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/astar.js/0.4.0/astar.min.js"></script>
    <style>
      body {
        margin: 0;
        padding: 0;
      }
      canvas {
        display: block;
      }
      .circle {
        position: absolute;
        width: 20px;
        height: 20px;
        border-radius: 50%;
        background-color: white;
      }
      .selected {
        background-color: red;
      }
    </style>
  </head>
  <body>
    <script>
      // Define variables
      let locations = [];
      let shortestPath = [];
      let pathLength = 0;

      function setup() {
        // Create canvas
        createCanvas(400, 400);

        // Draw circles to represent locations
        for (let i = 0; i < 5; i++) {
          let x = random(width);
          let y = random(height);
          locations.push(createVector(x, y));
          let circle = document.createElement('div');
          circle.classList.add('circle');
          circle.style.left = x - 10 + 'px';
          circle.style.top = y - 10 + 'px';
          circle.addEventListener('click', function() {
            if (this.classList.contains('selected')) {
              this.classList.remove('selected');
              locations[i].selected = false;
            } else {
              this.classList.add('selected');
              locations[i].selected = true;
            }
          });
          document.body.appendChild(circle);
        }
      }

      function draw() {
        // Clear canvas
        background(0);

        // Draw circles to represent locations
        for (let i = 0; i < locations.length; i++) {
          noStroke();
          if (locations[i].selected) {
            fill(255, 0, 0);
          } else {
            fill(255);
          }
          ellipse(locations[i].x, locations[i].y, 20, 20);
        }

        // Find shortest path between selected locations
        let selectedLocations = locations.filter(location => location.selected);
        if (selectedLocations.length > 1) {
          let graph = new Graph(selectedLocations);
          let start = selectedLocations[0];
          let end = selectedLocations[selectedLocations.length - 1];
          let result = astar.search(graph, start, end);
          shortestPath = result.map(node => node.location);
          pathLength = 0;
          for (let i = 0; i < shortestPath.length - 1; i++) {
            pathLength += dist(shortestPath[i].x, shortestPath[i].y, shortestPath[i+1].x, shortestPath[i+1].y);
          }
        }

        // Draw shortest path
        stroke(255, 0, 0);
        strokeWeight(3);
        noFill();
        beginShape();
        for (let i = 0; i < shortestPath.length; i++) {
          vertex(shortestPath[i].x, shortestPath[i].y);
        }
        endShape();
      }
    </script>
  </body>
</html>