<script>
    import actionsJson from "$lib/data/actions.json";
    import descriptorsJson from "$lib/data/descriptors.json";
    import subjectsJson from "$lib/data/subjects.json";
    import othersJson from "$lib/data/others.json";
    import Data from "$lib/data.svelte.js";

    let { actions, reagents, namespace } = Data

    let { word = $bindable(""), items } = $props()

    let searchableActions = actionsJson.concat(actions.map(a => {
        return `action.${namespace}.${a.id}`
    }))
    let searchableSubjects = subjectsJson
    let searchableDescriptors = descriptorsJson.concat(reagents.map(r => {
        return `reagent.${namespace}.${r.id}`
    }))
    let searchable = $derived(
        searchableActions.map(it => `action:${it}`).concat(
            searchableSubjects.map(it => `subject:${it}`).concat(
                searchableDescriptors.map(it => `descriptor:${it}`).concat(
                    othersJson.map(it => `other:${it}`)
                )
            )
        )
    )
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
    <input type="text" class="w-full p-2" bind:value={word} oninput={() => updating = true}>
    {#if word.trim().length > 0 && updating}
        <div class="w-full">
            {#each (items ?? searchable).filter(value => {
                return value.includes(word)
            }).slice(0, 10) as row}
                <button class="odd:bg-gray-100 w-full p-1 text-lg button-sm" onclick={() => {updating = false; word = row}}>
                    {row}
                </button>
            {/each}
        </div>
    {/if}
</div>