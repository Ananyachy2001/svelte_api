<script>
import Button from "./Button.svelte";
export let count;

</script>
<div class="Card">
    {count}
    <Button />
</div>
<style>
    .Card{
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        align-items: center;
        height: 300px;
        width: 200px;
        border: dotted hotpink 3px;
    }
</style>