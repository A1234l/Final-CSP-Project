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
        let lineEnd = { x: 0, y: 0 };
        let draggedNode = null;
        let snappedNode = null;
        let isDragging = false;
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
                lineEnd = { x: draggedNode.x, y: draggedNode.y };
                isDragging = true;
            }
        }
        function handleMouseUp() {
            if (isDragging) {
                if (snappedNode) {
                    // Draw a line from the dragged node to the snapped node
                    ctx.beginPath();
                    ctx.moveTo(draggedNode.x, draggedNode.y);
                    ctx.lineTo(snappedNode.x, snappedNode.y);
                    ctx.stroke();
                }
                isDragging = false;
                draggedNode = null;
                snappedNode = null;
                lineStart = { x: 0, y: 0 };
                lineEnd = { x: 0, y: 0 };
            }
        }
        function handleMouseMove(event) {
            if (isDragging && draggedNode) {
                const rect = canvas.getBoundingClientRect();
                const mousePosition = {
                    x: event.clientX - rect.left,
                    y: event.clientY - rect.top
                };
                lineEnd.x = mousePosition.x;
                lineEnd.y = mousePosition.y;
                snappedNode = nodes.find(node => {
                    const dx = node.x - mousePosition.x;
                    const dy = node.y - mousePosition.y;
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    return distance < 10;
                });
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                ctx.drawImage(image, 0, 0, canvas.width, canvas.height);
                drawNodes(); // Draw nodes
                if (snappedNode) {
                    ctx.beginPath();
                    ctx.moveTo(lineStart.x, lineStart.y);
                    ctx.lineTo(snappedNode.x, snappedNode.y);
                    ctx.stroke();
                } else {
                    ctx.beginPath();
                    ctx.moveTo(lineStart.x, lineStart.y);
                    ctx.lineTo(lineEnd.x, lineEnd.y);
                    ctx.stroke();
                }
            }
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
