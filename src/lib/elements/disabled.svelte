<script>
    let { disabled = $bindable([]) } = $props();
</script>


<div>
    Disabled:
    <input type="checkbox" bind:checked="{disabled}">
    {#if disabled}
        <span class="text-sm text-red-700 ml-2">This will not be accessible in-game!</span>
    {/if}
</div>