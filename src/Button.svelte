<script>
import { getContext} from "svelte";
let incrementCount = getContext('incrementCount');
</script>

<button class="Button" on:click={incrementCount} >
    Increment Count
</button>

<style>

</style>