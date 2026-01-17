<script>
    import json from "$lib/data/subjects.json";
    import Data from "$lib/data.svelte.js";

    let { namespace } = Data

    let { subject = $bindable(""), items } = $props()
    let searchable = $derived(json)
    let updating = $state(false)

    function searchedItems() {
        let counter = 0;
        return items.filter(value => {
            if(counter++ > 10) return false
            value.startsWith(subject)
        })
    }
</script>

<div class="m-2">
    <input type="text" class="w-full p-2" bind:value={subject} oninput={() => updating = true}>
    {#if subject.trim().length > 0 && updating}
        <div class="w-full">
            {#each (items ?? searchable).filter(value => {
                return value.startsWith(subject)
            }).slice(0, 10) as row}
                <button class="odd:bg-gray-100 w-full p-1 text-lg button-sm" onclick={() => {updating = false; subject = row}}>
                    {row}
                </button>
            {/each}
        </div>
    {/if}
</div>