<script>
	import Canvas from "./Canvas.svelte";
    import Canvas2 from "./Canvas2.svelte";
	import Card from "./Card.svelte";
	import {setContext} from 'svelte';

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
	<Canvas2 />
</main>

<style>

</style>