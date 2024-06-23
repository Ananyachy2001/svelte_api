<script>
    import { onMount } from 'svelte';
  
    let canvas, context;
    let image = new Image();
    let imageProperties = {
      width: 0,
      height: 0,
      format: ''
    };
  
    // Function to load and display the image
    function loadImage(event) {
      const file = event.target.files[0];
      if (file) {
        const imageUrl = URL.createObjectURL(file);
        image.src = imageUrl;
        image.onload = () => {
          imageProperties.width = image.width;
          imageProperties.height = image.height;
          imageProperties.format = file.type.split('/')[1];
          drawImage();
        };
      }
    }
  
    // Function to draw the image on the canvas
    function drawImage() {
      if (context && image) {
        context.clearRect(0, 0, canvas.width, canvas.height);
        canvas.width = image.width;
        canvas.height = image.height;
        context.drawImage(image, 0, 0);
      }
    }
  
    // Initialize canvas context after the canvas is mounted
    onMount(() => {
      if (canvas) {
        context = canvas.getContext('2d');
      }
    });
  </script>
  
  <input type="file" accept="image/*" on:change={loadImage}>
  <canvas bind:this={canvas}></canvas> <!-- Correctly bind this to the canvas element -->
  <div>
    <strong>Image Properties:</strong>
    <p>Width: {imageProperties.width}px</p>
    <p>Height: {imageProperties.height}px</p>
    <p>Format: {imageProperties.format}</p>
  </div>
  
  

  <style>
    canvas {
      border: 1px solid black;
    }
    div {
      margin-top: 10px;
    }
  </style>
  