<script>
    import itemList from "$lib/data/items.json";
    let { item = $bindable(""), items } = $props();
    let updating = $state(false)

    function searchedItems() {
        let counter = 0;
        return items.filter(value => {
            if(counter++ > 10) return false
            value.startsWith(item)
        })
    }
</script>

<div class="m-2">
    <input type="text" class="w-full p-2" bind:value={item} oninput={() => updating = true}>
    {#if item.trim().length > 0 && updating}
        <div class="w-full">
            {#each (items ?? itemList).filter(value => {
                return value.startsWith(item) || value.split(":")[1].startsWith(item)
            }).slice(0, 10) as row}
                <button class="odd:bg-gray-100 w-full p-1 text-lg button-sm" onclick={() => {updating = false; item = row}}>
                    {row}
                </button>
            {/each}
        </div>
    {/if}
</div>