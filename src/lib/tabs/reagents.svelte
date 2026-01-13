<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import ItemSearch from "$lib/elements/item-search.svelte";

    let { reagents = $bindable([]) } = $props();
    let active = $state(0);

    function addReagent() {
        reagents = reagents.concat([{
            id: `reagent_${reagents.length + 1}`,
            disabled: false,
            item: "minecraft:cobblestone",
            cost: 10,
        }])
        active = reagents.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each reagents as reagent, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {reagents[index].name} [{ reagents[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addReagent}>
            + Add New Reagent
        </button>
    </div>
    <div class="bg-yellow-50 p-1 w-3/4">
        {#if active < (reagents.length)}
            <div class="px-2 mb-3">
                <Disabled bind:disabled={reagents[active].disabled} />
                <div class="w-full flex">
                    <div class="mt-3 mr-4">
                        ID (should be unique)
                        <input type="text" bind:value="{reagents[active].id}">
                    </div>
                    <div class="mt-3 mx-4">
                        <label for="cost">Cost: </label>
                        <input type="number" id="cost" step="0.1" bind:value={reagents[active].cost} >
                    </div>
                </div>
            </div>
            <div class="px-2 mb-3">
                Item:
            </div>
            <ItemSearch bind:item={reagents[active].item} />
        {:else}
            <h2 class="text-xl">Please select or create a reagent.</h2>
        {/if}
    </div>
</div>