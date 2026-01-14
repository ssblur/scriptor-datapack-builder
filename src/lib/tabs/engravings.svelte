<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import Spell from "$lib/elements/spell.svelte";

    let { engravings = $bindable([]) } = $props();
    let active = $state(0);

    function addEngraving() {
        engravings = engravings.concat([{
            id: `engraving_${engravings.length + 1}`,
            disabled: false,
            spell: {
                "subject": "self",
                "spells": [
                    {
                        "action": "clear_weather",
                        "descriptors": ["strong"]
                    }
                ]
            }
        }])
        active = engravings.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each engravings as engraving, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {engravings[index].name} [{ engravings[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addEngraving}>
            + Add New Engraving
        </button>
    </div>
    <div class="flex-grow-1 bg-yellow-50 p-1">
        {#if active < (engravings.length)}
            <Disabled bind:disabled={engravings[active].disabled} />
            <div class="px-4">
                ID (should be unique)
                <input type="text" bind:value="{engravings[active].id}">
            </div>
            <div class="px-4">
                <p>Spell:</p>
                <Spell bind:spell={engravings[active].spell} />
            </div>
        {:else}
            <h2 class="text-xl">Please select or create a custom engraving.</h2>
        {/if}
    </div>
</div>