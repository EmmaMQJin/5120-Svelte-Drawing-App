<script>
  import { onMount } from 'svelte';

  let canvas, ctx;
  let isDrawing = false;
  let color = '#000000';
  let lineWidth = 3;
  let isErasing = false;

  onMount(() => {
    canvas.width = 800 * 2;  // Using a scale factor for high resolution
    canvas.height = 600 * 2;
    canvas.style.width = '800px';
    canvas.style.height = '600px';

    ctx = canvas.getContext('2d');
    ctx.scale(2, 2);  // Adjusting for the high resolution
    ctx.lineCap = 'round';
    ctx.strokeStyle = color;
    ctx.lineWidth = lineWidth;
    ctx.globalCompositeOperation = 'source-over';  // Default to drawing mode
  });

  $: if (ctx) {
    ctx.strokeStyle = isErasing ? 'rgba(0,0,0,1)' : color;
    ctx.lineWidth = lineWidth;
    ctx.globalCompositeOperation = isErasing ? 'destination-out' : 'source-over';
  }

  function startDrawing(event) {
    const { offsetX, offsetY } = event;
    ctx.beginPath();
    ctx.moveTo(offsetX, offsetY);
    isDrawing = true;
  }

  function finishDrawing() {
    ctx.closePath();
    isDrawing = false;
  }

  function draw(event) {
    if (!isDrawing) return;
    const { offsetX, offsetY } = event;
    ctx.lineTo(offsetX, offsetY);
    ctx.stroke();
  }

  function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
  }

  function toggleEraser() {
    isErasing = !isErasing;
  }
</script>

<div class="App">
  <canvas
    bind:this={canvas}
    on:mousedown={startDrawing}
    on:mouseup={finishDrawing}
    on:mousemove={draw}
  />
  <input type="color" bind:value={color} disabled={isErasing} />
  <input type="range" min="1" max="10" bind:value={lineWidth} />
  <button on:click={clearCanvas}>Clear All</button>
  <button on:click={toggleEraser}>{isErasing ? 'Switch to Pencil' : 'Switch to Eraser'}</button>
</div>

<style>
  .App {
    text-align: center;
    margin-top: 20px;
  }
  canvas {
    border: 1px solid black;
    display: block;
    margin: 20px auto;
  }
</style>
