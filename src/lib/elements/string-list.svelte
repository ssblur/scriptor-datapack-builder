<script>
    let { value = $bindable([]) } = $props();

    function remove(index) {
        value = value.filter((_, i) => i != index)
    }

    function add() {
        value = value.concat([""])
    }
</script>

<div class="p-2">
    <div class="mx-2 w-full bg-gray-50">
        {#if value.length == 0}
            <div class="w-full odd:bg-gray-200 py-2 px-4 relative flex items-center">
                No entries added.
            </div>
        {/if}
        {#each value as item, index}
            <div class="w-full odd:bg-gray-200 py-2 px-4 relative flex items-center">
                <input type="text" class="flex-grow mr-2" bind:value={value[index]}>
                <button class="button-sm danger" onclick={() => remove(index)}>Remove</button>
            </div>
        {/each}
    </div>
    <button class="primary w-full m-2" onclick={add}>Add</button>
</div>