<script>
    import { onMount } from 'svelte';
  
    let canvas;
    let ctx;
    let drawing = false;
    let lastX = 0;
    let lastY = 0;
    let color = 'black'; // Default drawing color
    let brushSize = 5; // Default brush size
  
    onMount(() => {
      if (canvas) {
        ctx = canvas.getContext('2d');
        canvas.width = 600;
        canvas.height = 500;
        drawShapes();
        addEventListeners();
      }
    });
  
    function drawShapes() {
      // Clear canvas
      ctx.clearRect(0, 0, canvas.width, canvas.height);
  
      // Draw coordinate system lines
      drawCoordinateSystem();
  
      // Draw some predefined shapes
      drawRectangle(30, 30, 100, 100);
      drawCircle(200, 200, 50);
      drawLine(320, 320, 400, 400);
    }
  
    function drawCoordinateSystem() {
      ctx.strokeStyle = 'lightgray';
      // Draw horizontal and vertical lines
      for (let i = 0; i <= canvas.height; i += 50) {
        ctx.beginPath();
        ctx.moveTo(0, i);
        ctx.lineTo(canvas.width, i);
        ctx.stroke();
      }
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
  
    function addEventListeners() {
      canvas.addEventListener('mousedown', (e) => {
        drawing = true;
        [lastX, lastY] = [e.offsetX, e.offsetY];
      });
  
      canvas.addEventListener('mousemove', (e) => {
        if (!drawing) return;
        ctx.lineWidth = brushSize;
        ctx.strokeStyle = color;
        ctx.beginPath();
        ctx.moveTo(lastX, lastY);
        [lastX, lastY] = [e.offsetX, e.offsetY];
        ctx.lineTo(lastX, lastY);
        ctx.stroke();
      });
  
      canvas.addEventListener('mouseup', () => drawing = false);
      canvas.addEventListener('mouseout', () => drawing = false);
    }
  
    function clearCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }
  </script>
  
  <canvas bind:this={canvas}></canvas>
  <button on:click={clearCanvas}>Clear</button>
  <label>
    Color:
    <input type="color" bind:value={color}>
  </label>
  <label>
    Brush Size:
    <input type="range" min="1" max="20" bind:value={brushSize}>
  </label>
  <style>
    canvas {
      border: 1px solid black;
    }
    button, label {
      margin-top: 10px;
    }
  </style>
  