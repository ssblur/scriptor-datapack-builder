<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import Spell from "$lib/elements/spell.svelte";
    import spellbooks from "$lib/data/spellbooks.json";
	import ItemSearch from "$lib/elements/item-search.svelte";

    let { tomes = $bindable([]) } = $props();
    let active = $state(0);

    function addTome() {
        tomes = tomes.concat([{
            id: `tome_${tomes.length + 1}`,
            name: "New Tome",
            author: "Me!",
            disabled: false,
            tier: 1,
            item: "scriptor:spellbook",
            spell: {
                "subject": "self",
                "spells": [
                    {
                        "action": "heal",
                        "descriptors": []
                    }
                ]
            },
        }])
        active = tomes.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each tomes as tome, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {tomes[index].name} [{ tomes[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addTome}>
            + Add New Tome
        </button>
    </div>
    <div class="w-3/4 bg-yellow-50 p-1">
        {#if active < (tomes.length)}
            <div class="px-4 text-lg">
                <Disabled bind:disabled={tomes[active].disabled} />
            </div>
            <div class="px-4">
                <p class="text-lg">ID (must be unique):</p>
                <input type="text" class="mx-2 w-1/2" bind:value={tomes[active].id}/>
            </div>
            <div class="px-4">
                <p class="text-lg">Name (either direct or a localization key):</p>
                <input type="text" class="mx-2 w-1/2" bind:value={tomes[active].name}/>
            </div>
            <div class="px-4">
                <p class="text-lg">Author Name:</p>
                <input type="text" class="mx-2 w-1/2" bind:value={tomes[active].author}/>
            </div>
            <div class="px-4">
                <p class="text-lg">Tome Tier:</p>
                <input type="number" class="mx-2" bind:value={tomes[active].tier}/>
            </div>
            <div class="px-4">
                <p class="text-lg">Spellbook:</p>
                <ItemSearch bind:item={tomes[active].item} items={spellbooks} />
            </div>
            <div class="px-4">
                <p class="text-lg">Spell:</p>
                <Spell bind:spell={tomes[active].spell} />
            </div>
        {:else}
            <h2 class="text-xl">Please select or create a tome.</h2>
        {/if}
    </div>
</div>