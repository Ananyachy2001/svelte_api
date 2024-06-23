<script>
import { getContext} from "svelte";
let incrementCount = getContext('incrementCount');
</script>

<button class="Button" on:click={incrementCount} >
    increment Count
</button>

<style>

</style>