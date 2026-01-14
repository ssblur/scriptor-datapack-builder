<script>
	import ActionSearch from "./action-search.svelte";
	import DescriptorSearch from "./descriptor-search.svelte";
	import SubjectSearch from "./subject-search.svelte";

    let { spell = $bindable({}) } = $props();

    function removeDescriptor(index, descriptorIndex) {
        spell.spells[index].descriptors = spell.spells[index].descriptors.filter((_, i) => i != descriptorIndex)

        spell = spell
    }

    function addDescriptor(index) {
        spell.spells[index].descriptors.push("stacking_strong")

        spell = spell
    }

    function addSpellPart() {
        spell.spells.push({
            action: "inflame",
            descriptors: [
                "stacking_strong"
            ]
        })

        spell = spell
    }

    function removeSpellPart(index) {
        spell.spells = spell.spells.filter((_, i) => i != index)
    }
</script>

<div class="text-sm w-full">
    <div class="p-2 w-full">
        <p class="text-red-700">The spell creator is not yet finished. Thank you for your patience.</p>
    </div>
    <div class="py-2 flex w-full">
        <div class="p-4 text-lg w-1/2">
            <b>Subject:</b>
        </div>
        <div class="px-4 w-1/2">
            <SubjectSearch bind:subject="{spell.subject}" />
        </div>
    </div>

    {#if spell.spells.length == 0} 
        <div class="py-2 flex w-full">
            <div class="p-4 text-lg text-red-700">
                <b>Spells must have at least 1 part!</b>
            </div>
        </div>
    {/if}

    {#each spell.spells as partial, index}
        <div class="pl-2 border-1 odd:bg-gray-100 even:bg-white mb-2 w-full">
            <div class="flex w-full">
                <div class="p-4 w-1/4 text-lg">
                    <b>Action:</b>
                </div>
                <div class="px-4 w-1/2">
                    <ActionSearch bind:action="{spell.spells[index].action}" />
                </div>
                <div class="p-3 w-1/4">
                    <button class="button-lg danger" onclick={() => removeSpellPart(index)}>Remove</button>
                </div>
            </div>

            <div class="w-full">
                <div class="flex">
                    <div class="p-4 w-1/2 text-lg">
                        <b>Descriptors:</b>
                    </div>
                </div>
                {#each partial.descriptors as descriptor, descriptorIndex}
                    <div class="w-full flex">
                        <div class="p-1 w-3/4">
                            <DescriptorSearch bind:descriptor="{spell.spells[index].descriptors[descriptorIndex]}" />
                        </div>
                        <div class="p-4 w-1/4">
                            <button class="button-lg danger" onclick={() => removeDescriptor(index, descriptorIndex)}>Remove</button>
                        </div>
                    </div>
                {/each}

                <div class="w-full flex pl-3 pb-2">
                    <button class="button-lg primary" onclick={() => addDescriptor(index)}>Add Descriptor</button>
                </div>
            </div>
        </div>
    {/each}

    <div class="w-full">
        <button class="button-lg primary" onclick={() => addSpellPart()}>Add Spell Part</button>
    </div>
</div>