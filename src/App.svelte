<script>
	import Canvas from "./Canvas.svelte";
	import Card from "./Card.svelte";
	import {setContext} from 'svelte';
    import ImageCanvas from "./ImageCanvas.svelte";

	let count = 0;

	function incrementCount(){
		count++;
	}

	setContext('incrementCount',incrementCount);
</script>

<main>
	<Card {count}  />

	<h1>Canvas Drawing with Svelte</h1>
	<Canvas />
	<!-- <Canvas2 /> -->

	<h1>Canvas Image:</h1>
	<ImageCanvas />
</main>

<style>

</style>