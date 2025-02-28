<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import Spell from "$lib/elements/spell.svelte";

    let { artifacts = $bindable([]) } = $props();
    let active = $state(0);

    function addArtifact() {
        artifacts = artifacts.concat([{
            id: `artifact_${artifacts.length + 1}`,
            name: "New Artifact",
            disabled: false,
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
        active = artifacts.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each artifacts as artifact, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {artifacts[index].name} [{ artifacts[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addArtifact}>
            + Add New Artifact
        </button>
    </div>
    <div class="flex-grow-1 bg-yellow-50 p-1">
        {#if active < (artifacts.length)}
            <Disabled bind:disabled={artifacts[active].disabled} />
            <div class="py-2 flex">
                <div class="px-4">
                    Name
                    <input type="text" bind:value="{artifacts[active].name}">
                </div>
                <div class="px-4">
                    ID (should be unique)
                    <input type="text" bind:value="{artifacts[active].id}">
                </div>
            </div>
            <div class="py-2">
                <div class="px-4">
                    Spell: 
                    <Spell bind:spell={artifacts[active].spell} />
                </div>
            </div>
        {:else}
            <h2 class="text-xl">Please select or create an artifact.</h2>
        {/if}
    </div>
</div>