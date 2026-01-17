<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import ItemSearch from "$lib/elements/item-search.svelte";

    let { scraps = $bindable([]) } = $props();
    let active = $state(0);

    function addScrap() {
        scraps = scraps.concat([{
            id: `scrap_${scraps.length + 1}`,
            disabled: false,
            tier: 1,
            keys: [],
        }])
        active = scraps.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each scraps as scrap, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                [{ scraps[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addScrap}>
            + Add New Scrap
        </button>
    </div>
    <div class="flex-grow-1 bg-yellow-50 p-1">
        {#if active < (scraps.length)}
            <Disabled bind:disabled={scraps[active].disabled} />
            <br>
            <div class="text-lg text-red-600">Scraps aren't implemented in this editor yet, thanks for your patience.</div>
        {:else}
            <h2 class="text-xl">Please select or create a scrap.</h2>
        {/if}
    </div>
</div>