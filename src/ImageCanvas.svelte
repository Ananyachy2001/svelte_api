<script>
    import { onMount } from 'svelte';

    let canvas, context;
    let offscreenCanvas = document.createElement('canvas');
    let offscreenContext = offscreenCanvas.getContext('2d');
    let image = new Image();
    let imageProperties = {
        width: 0,
        height: 0,
        format: ''
    };
    let rotation = 0;
    let scale = 1;
    let filter = '';
    let crop = {
        x: 0,
        y: 0,
        width: 100,
        height: 100,
        enabled: false
    };

    let history = [];
    let historyIndex = -1;

    function saveState() {
        if (historyIndex + 1 < history.length) {
            history.splice(historyIndex + 1);
        }
        history.push(JSON.stringify({
            imageSrc: image.src,
            rotation,
            scale,
            filter,
            crop: { ...crop }
        }));
        historyIndex++;
    }

    function undo() {
        if (historyIndex > 0) {
            historyIndex--;
            loadState(JSON.parse(history[historyIndex]));
        }
    }

    function redo() {
        if (historyIndex < history.length - 1) {
            historyIndex++;
            loadState(JSON.parse(history[historyIndex]));
        }
    }

    function loadState(state) {
        image.src = state.imageSrc;
        rotation = state.rotation;
        scale = state.scale;
        filter = state.filter;
        crop = { ...state.crop };
        drawImage();
    }

    function loadImage(event) {
        const file = event.target.files[0];
        if (file) {
            const imageUrl = URL.createObjectURL(file);
            image.src = imageUrl;
            image.onload = () => {
                imageProperties.width = image.width;
                imageProperties.height = image.height;
                imageProperties.format = file.type.split('/')[1];
                crop.width = image.width;
                crop.height = image.height;
                drawImage();
                saveState();
            };
        }
    }

    function drawImage() {
        if (context && image) {
            context.clearRect(0, 0, canvas.width, canvas.height);
            if (!crop.enabled) {
                canvas.width = image.width * scale;
                canvas.height = image.height * scale;
            } else {
                canvas.width = crop.width * scale;
                canvas.height = crop.height * scale;
            }
            context.save();
            context.translate(canvas.width / 2, canvas.height / 2);
            context.rotate((Math.PI / 180) * rotation);
            context.scale(scale, scale);
            context.filter = filter;
            if (!crop.enabled) {
                context.drawImage(image, -image.width / 2, -image.height / 2);
            } else {
                context.drawImage(image, crop.x, crop.y, crop.width, crop.height, -crop.width / 2, -crop.height / 2, crop.width, crop.height);
            }
            context.restore();
        }
    }

    onMount(() => {
        if (canvas) {
            context = canvas.getContext('2d');
        }
    });

    function rotate(degrees) {
        rotation += degrees;
        drawImage();
        saveState();
    }

    function changeScale(factor) {
        scale *= factor;
        drawImage();
        saveState();
    }

    function applyFilter(selectedFilter) {
        filter = selectedFilter;
        drawImage();
        saveState();
    }

    function enableCrop() {
        crop.enabled = true;
        drawImage();
        saveState();
    }

    function disableCrop() {
        crop.enabled = false;
        drawImage();
        saveState();
    }

    function updateCrop(x, y, width, height) {
        crop.x = x;
        crop.y = y;
        crop.width = width;
        crop.height = height;
        drawImage();
        saveState();
    }

    function resetTransformations() {
        rotation = 0;
        scale = 1;
        filter = '';
        crop = { x: 0, y: 0, width: image.width, height: image.height, enabled: false };
        drawImage();
        saveState();
    }
</script>

<input type="file" accept="image/*" on:change={loadImage}>
<canvas bind:this={canvas}></canvas>
<div>
    <strong>Image Properties:</strong>
    <p>Width: {imageProperties.width}px</p>
    <p>Height: {imageProperties.height}px</p>
    <p>Format: {imageProperties.format}</p>
</div>
<button on:click={undo}>Undo</button>
<button on:click={redo}>Redo</button>
<button on:click={() => rotate(90)}>Rotate 90°</button>
<button on:click={() => changeScale(1.1)}>Scale Up</button>
<button on:click={() => changeScale(0.9)}>Scale Down</button>
<button on:click={() => applyFilter('grayscale(100%)')}>Grayscale</button>
<button on:click={() => applyFilter('sepia(100%)')}>Sepia</button>
<button on:click={enableCrop}>Enable Crop</button>
<button on:click={disableCrop}>Disable Crop</button>
<button on:click={() => updateCrop(50, 50, 200, 200)}>Crop 200x200 from (50,50)</button>
<button on:click={resetTransformations}>Reset</button>

<style>
    canvas {
        border: 1px solid black;
    }
    div, button {
        margin-top: 10px;
    }
</style>
