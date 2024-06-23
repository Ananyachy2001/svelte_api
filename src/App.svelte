<script>
	import Card from "./Card.svelte";
	import {setContext} from 'svelte';

	let count = 0;

	function incrementCount(){
		count++;
	}

	setContext('incrementCount',incrementCount);
</script>

<main>
	<Card count={count}  />
</main>

<style>

</style>