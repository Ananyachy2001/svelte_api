<script>
    import { onMount } from 'svelte';

    let canvas, context;
    let image = new Image();
    let imageProperties = { width: 0, height: 0, format: '' };
    let rotation = 0;
    let scale = 1;
    let bgColor = '#FFFFFF';
    let filter = '';
    let crop = {
        x: 0, y: 0, width: 100, height: 100, enabled: false
    };

    let history = [];
    let historyIndex = -1;

    function saveState() {
        if (historyIndex + 1 < history.length) {
            history.splice(historyIndex + 1);
        }
        history.push(JSON.stringify({ imageSrc: image.src, rotation, scale, filter, crop, bgColor }));
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
        bgColor = state.bgColor;
        drawImage();
    }

    function loadImage(event) {
        const file = event.target.files[0];
        if (file) {
            image.src = URL.createObjectURL(file);
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
        if (context) {
            context.clearRect(0, 0, canvas.width, canvas.height);
            context.fillStyle = bgColor;
            context.fillRect(0, 0, canvas.width, canvas.height);
            canvas.width = image.width * scale;
            canvas.height = image.height * scale;
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
        context = canvas.getContext('2d');
        drawImage();
    });

    function updateRotation(event) {
        rotation = parseInt(event.target.value);
        drawImage();
        saveState();
    }

    function updateScale(event) {
        scale = parseFloat(event.target.value);
        drawImage();
        saveState();
    }

    function updateBgColor(event) {
        bgColor = event.target.value;
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
        bgColor = '#FFFFFF';
        crop = { x: 0, y: 0, width: image.width, height: image.height, enabled: false };
        drawImage();
        saveState();
    }

    function handleDragOver(event) {
    event.preventDefault(); // Prevent default behavior (Prevent file from being opened)
}

function handleDrop(event) {
    event.preventDefault();
    const file = event.dataTransfer.files[0];
    loadImageFromFile(file);
}

function loadImageFromFile(file) {
    if (file && file.type.startsWith('image/')) {
        image.src = URL.createObjectURL(file);
        image.onload = setupImage;
    }
}

</script>

<input type="file" accept="image/*" on:change={loadImage}>
<canvas bind:this={canvas}></canvas>

<div class="drop-area" on:dragover="{handleDragOver}" on:drop="{handleDrop}">
    Drag and drop an image here
</div>
<div class="control-group">
    <label for="rotationSlider">Rotation</label>
    <input type="range" id="rotationSlider" min="-180" max="180" value="{rotation}" on:input="{updateRotation}">
</div>
<div class="control-group">
    <label for="scaleSlider">Scale</label>
    <input type="range" id="scaleSlider" min="0.1" max="2" step="0.1" value="{scale}" on:input="{updateScale}">
</div>
<div class="control-group">
    <label for="bgColorPicker">Background Color</label>
    <input type="color" id="bgColorPicker" value="{bgColor}" on:change="{updateBgColor}">
</div>
<button on:click={undo}>Undo</button>
<button on:click={redo}>Redo</button>
<button on:click={() => applyFilter('grayscale(100%)')}>Grayscale</button>
<button on:click={() => applyFilter('sepia(100%)')}>Sepia</button>
<button on:click={enableCrop}>Enable Crop</button>
<button on:click={disableCrop}>Disable Crop</button>
<button on:click={() => updateCrop(50, 50, 200, 200)}>Update Crop</button>
<button on:click={resetTransformations}>Reset All</button>

<style>
    canvas {
        border: 1px solid black;
        margin-top: 10px;
    }
    .control-group {
        margin-top: 10px;
    }


    .drop-area {
    border: 2px dashed #aaa; /* Dashed border */
    border-radius: 5px;
    padding: 20px;
    text-align: center;
    color: #aaa;
    font-size: 16px;
    transition: border-color 0.3s, background-color 0.3s;
    margin-top: 10px;
}

.drop-area:hover {
    border-color: #666; /* Darker border on hover */
    background-color: #f8f8f8; /* Light background on hover */
}

</style>
