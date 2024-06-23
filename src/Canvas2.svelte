<script>
    import { onMount } from 'svelte';
  
    let canvas, ctx;
    let drawing = false;
    let lastX = 0;
    let lastY = 0;
  
    const startDrawing = (e) => {
      drawing = true;
      [lastX, lastY] = [e.offsetX, e.offsetY];
    };
  
    const draw = (e) => {
      if (!drawing) return;
      ctx.beginPath();
      ctx.moveTo(lastX, lastY);
      [lastX, lastY] = [e.offsetX, e.offsetY];
      ctx.lineTo(lastX, lastY);
      ctx.stroke();
    };
  
    const stopDrawing = () => {
      drawing = false;
    };
  
    onMount(() => {
      ctx = canvas.getContext('2d');
      ctx.strokeStyle = "#000000";
      ctx.lineJoin = 'round';
      ctx.lineCap = 'round';
      ctx.lineWidth = 5;
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
  
      canvas.addEventListener('mousedown', startDrawing);
      canvas.addEventListener('mousemove', draw);
      canvas.addEventListener('mouseup', stopDrawing);
      canvas.addEventListener('mouseout', stopDrawing);
    });
  </script>
  
  <canvas bind:this={canvas}></canvas>
  
  <style>
    canvas {
      position: fixed;
      top: 0;
      left: 0;
    }
  </style>
  