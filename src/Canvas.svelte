<script>
    import { onMount } from 'svelte';
  
    let canvas;
    let ctx;
  
    onMount(() => {
      if (canvas) {
        ctx = canvas.getContext('2d');
        drawShapes();
      }
    });
  
    function drawShapes() {
      if (!ctx) return;
  
      // Clear canvas
      ctx.clearRect(0, 0, canvas.width, canvas.height);
  
      // Draw coordinate system lines
      drawCoordinateSystem();
  
      // Draw some shapes
      drawRectangle(30, 30, 100, 100);
      drawCircle(200, 200, 50);
      drawLine(320, 320, 400, 400);
    }
  
    function drawCoordinateSystem() {
      ctx.strokeStyle = 'lightgray';
  
      // Draw horizontal lines
      for (let i = 0; i <= canvas.height; i += 50) {
        ctx.beginPath();
        ctx.moveTo(0, i);
        ctx.lineTo(canvas.width, i);
        ctx.stroke();
      }
  
      // Draw vertical lines
      for (let i = 0; i <= canvas.width; i += 50) {
        ctx.beginPath();
        ctx.moveTo(i, 0);
        ctx.lineTo(i, canvas.height);
        ctx.stroke();
      }
  
      ctx.strokeStyle = 'black';
    }
  
    function drawRectangle(x, y, width, height) {
      ctx.fillStyle = 'blue';
      ctx.fillRect(x, y, width, height);
    }
  
    function drawCircle(x, y, radius) {
      ctx.fillStyle = 'green';
      ctx.beginPath();
      ctx.arc(x, y, radius, 0, Math.PI * 2);
      ctx.fill();
    }
  
    function drawLine(x1, y1, x2, y2) {
      ctx.strokeStyle = 'red';
      ctx.beginPath();
      ctx.moveTo(x1, y1);
      ctx.lineTo(x2, y2);
      ctx.stroke();
    }
  </script>
  
  <style>
    canvas {
      border: 1px solid black;
    }
  </style>
  
  <canvas bind:this={canvas} width="600" height="500"></canvas>
  