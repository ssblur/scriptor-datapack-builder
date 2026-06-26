<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import Spell from "$lib/elements/spell.svelte";
    import itemData from "$lib/data/items.json";
	import ItemSearch from "$lib/elements/item-search.svelte";

    let { items = $bindable([]) } = $props();
    let active = $state(0);

    function addItem() {
        items = items.concat([{
            id: `item_${items.length + 1}`,
            name: "New Item",
            disabled: false,
            item: "minecraft:stone_sword",
            mob: "minecraft:zombie",
            chance: 0.1,
            slot: "MAINHAND",
            rarity: "COMMON",
            spell: {
                "subject": "on_hit",
                "spells": [
                    {
                        "action": "harm",
                        "descriptors": []
                    }
                ]
            },
        }])
        active = items.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each items as item, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {items[index].name} [{ items[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addItem}>
            + Add New Item
        </button>
    </div>
    <div class="w-3/4 bg-yellow-50 p-1">
        {#if active < (items.length)}
            <div class="px-4 text-lg">
                <Disabled bind:disabled={items[active].disabled} />
            </div>
            <div class="px-4">
                <p class="text-lg">ID (must be unique):</p>
                <input type="text" class="mx-2 w-1/2" bind:value={items[active].id}/>
            </div>
            <div class="px-4">
                <p class="text-lg">Name (either direct or a localization key):</p>
                <input type="text" class="mx-2 w-1/2" bind:value={items[active].name}/>
            </div>
            <div class="px-4">
                <p class="text-lg">Item:</p>
                <ItemSearch bind:item={items[active].item} items={itemData} />
            </div>
            <div class="px-4">
                <p class="text-lg">Entity ID:</p>
                <input type="text" bind:value={items[active].mob}>
            </div>
            <div class="px-4">
                <p class="text-lg">Chance:</p>
                <input type="number" min="0" max="1" step="0.01" bind:value={items[active].chance}>
            </div>
            <div class="px-4">
                <p class="text-lg">Equipment Slot:</p>
                <select bind:value={items[active].slot}>
                    <option value="MAINHAND">Main Hand</option>
                    <option value="OFFHAND">Off Hand</option>
                    <option value="HEAD">Helmet</option>
                    <option value="CHEST">Chestplate</option>
                    <option value="LEGS">Leggings</option>
                    <option value="FEET">Feet</option>
                </select>
            </div>
            <div class="px-4">
                <p class="text-lg">Rarity</p>
                <select bind:value={items[active].rarity}>
                    <option value="COMMON">Common</option>
                    <option value="UNCOMMON">Uncommon</option>
                    <option value="RARE">Rare</option>
                    <option value="EPIC">Epic</option>
                </select>
            </div>
            <div class="px-4">
                <p class="text-lg">Spell:</p>
                <Spell bind:spell={items[active].spell} />
            </div>
        {:else}
            <h2 class="text-xl">Please select or create a item.</h2>
        {/if}
    </div>
</div>