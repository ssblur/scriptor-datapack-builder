<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import ItemSearch from "$lib/elements/item-search.svelte";
	import StringList from "$lib/elements/string-list.svelte";

    let { notes = $bindable([]) } = $props();
    let active = $state(0);

    function addScrap() {
        notes = notes.concat([{
            id: `note_${notes.length + 1}`,
            disabled: false,
            tier: 1,
            keys: [],
        }])
        active = notes.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each notes as scrap, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                [{ notes[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addScrap}>
            + Add New Notes
        </button>
    </div>
    <div class="flex-grow-1 bg-yellow-50 p-1">
        {#if active < (notes.length)}
            <Disabled bind:disabled={notes[active].disabled} />
            <br/>
            Tier: <input type="number" bind:value={notes[active].tier} min="0" max="2"/>
            <br/>
            Notes:
            <br/>
            <StringList bind:value={notes[active].keys} />
        {:else}
            <h2 class="text-xl">Please select or create a set of notes.</h2>
        {/if}
    </div>
</div>