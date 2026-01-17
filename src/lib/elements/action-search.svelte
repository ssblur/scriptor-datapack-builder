<script>
    import json from "$lib/data/actions.json";
    import Data from "$lib/data.svelte.js";

    let { actions, namespace } = Data

    let { action = $bindable(""), items } = $props()

    let searchable = $derived(json.concat(actions.map(a => {
        return `action.${namespace}.${a.id}`
    })))
    let updating = $state(false)

    function searchedItems() {
        let counter = 0;
        return items.filter(value => {
            if(counter++ > 10) return false
            value.startsWith(action)
        })
    }
</script>

<div class="m-2">
    <input type="text" class="w-full p-2" bind:value={action} oninput={() => updating = true}>
    {#if action.trim().length > 0 && updating}
        <div class="w-full">
            {#each (items ?? searchable).filter(value => {
                return value.startsWith(action)
            }).slice(0, 10) as row}
                <button class="odd:bg-gray-100 w-full p-1 text-lg button-sm" onclick={() => {updating = false; action = row}}>
                    {row}
                </button>
            {/each}
        </div>
    {/if}
</div>