<!DOCTYPE html>
<html>
<head>
    <style>
        #canvas {
            border: 1px solid #000;
        }
    </style>
</head>
<body>
    <canvas id="canvas" width="1072" height="829"></canvas>
    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        const image = new Image();
        image.src = 'SDmap.png';
        image.addEventListener('load', function() {
            ctx.drawImage(image, 0, 0, canvas.width, canvas.height);
            drawNodes(); // Draw nodes once the image is loaded
        });
        const nodes = [
            { x: 100, y: 100 },
            { x: 200, y: 200 },
            { x: 300, y: 300 }
        ];
        let lineStart = { x: 0, y: 0 };
        let draggedNode = null;
        canvas.addEventListener('mousedown', handleMouseDown);
        canvas.addEventListener('mouseup', handleMouseUp);
        canvas.addEventListener('mousemove', handleMouseMove);
        function handleMouseDown(event) {
            const rect = canvas.getBoundingClientRect();
            const mousePosition = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            };
            // Check if the mouse is starting on a node
            draggedNode = nodes.find(node => {
                const dx = node.x - mousePosition.x;
                const dy = node.y - mousePosition.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                return distance < 10;
            });
            if (draggedNode) {
                lineStart = { x: draggedNode.x, y: draggedNode.y };
            }
        }
        function handleMouseUp() {
            if (draggedNode) {
                const hoveredNode = nodes.find(node => node !== draggedNode);
                if (hoveredNode) {
                    // Draw a line from the dragged node to the hovered node
                    ctx.beginPath();
                    ctx.moveTo(draggedNode.x, draggedNode.y);
                    ctx.lineTo(hoveredNode.x, hoveredNode.y);
                    ctx.stroke();
                }
            }
            draggedNode = null;
            lineStart = { x: 0, y: 0 };
        }
        function handleMouseMove(event) {
            if (!draggedNode) {
                return;
            }
            const rect = canvas.getBoundingClientRect();
            const lineEnd = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            };
            const hoveredNode = nodes.find(node => node !== draggedNode);
            if (hoveredNode) {
                // Lock the line to the nearest node
                lineEnd.x = hoveredNode.x;
                lineEnd.y = hoveredNode.y;
            }
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(image, 0, 0, canvas.width, canvas.height);
            drawNodes(); // Draw nodes
            ctx.beginPath();
            ctx.moveTo(lineStart.x, lineStart.y);
            ctx.lineTo(lineEnd.x, lineEnd.y);
            ctx.stroke();
        }
        function drawNodes() {
            nodes.forEach(node => {
                ctx.beginPath();
                ctx.arc(node.x, node.y, 5, 0, 2 * Math.PI);
                ctx.fillStyle = 'black';
                ctx.fill();
            });
        }
    </script>
</body>
</html>
