<script>
    import json from "$lib/data/descriptors.json";
    import Data from "$lib/data.svelte.js";

    let { reagents, namespace } = Data

    let { descriptor = $bindable(""), items } = $props();
    let searchable = $derived(json.concat(reagents.map(r => {
        return `reagent.${namespace}.${r.id}`
    })))
    let updating = $state(false)

    function searchedItems() {
        let counter = 0;
        return items.filter(value => {
            if(counter++ > 10) return false
            value.startsWith(descriptor)
        })
    }
</script>

<div class="m-2">
    <input type="text" class="w-full p-2" bind:value={descriptor} oninput={() => updating = true}>
    {#if descriptor.trim().length > 0 && updating}
        <div class="w-full">
            {#each (items ?? searchable).filter(value => {
                return value.split(".").some(it => it.startsWith(descriptor))
            }).slice(0, 10) as row}
                <button class="odd:bg-gray-100 w-full p-1 text-lg button-sm" onclick={() => {updating = false; descriptor = row}}>
                    {row}
                </button>
            {/each}
        </div>
    {/if}
</div>