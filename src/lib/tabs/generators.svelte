<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import ItemSearch from "$lib/elements/item-search.svelte";
	import StringList from "$lib/elements/string-list.svelte";

    let { generators = $bindable([]) } = $props();
    let active = $state(0);

    function addMixedGroupGenerator() {
        generators = generators.concat([{
            id: `generator_${generators.length + 1}`,
            generator: "mixed_groups",
            disabled: false,
            default: !generators.some(it => it.default),
            parameters: {
                min_tokens: 3,
                max_tokens: 6,
                max_consecutive_groups: 1,
                groups: [
                    {
                        weight: 1, 
                        tokens: [
                            "b", "c", "d", "e", "f", "g", "h", "j", "k", "l", "m", "n", "p", "r", "s", "t", "v", "w", "x", "z",
                            "st", "th", "ld", "qu", "pr", "lk", "mm", "pp", "ck", "rd", "pt"
                        ]
                    },
                    {
                        weight: 1, 
                        tokens: [
                            "a", "e", "i", "o", "u", "ou", "ao", "io", "ee"
                        ]
                    }
                ]
            },
        }])
        active = generators.length - 1
    }

    function addTokenGroup() {
        generators[active].parameters.groups.push({weight: 1, tokens: []})
    }

    function addStaticTokenGenerator() {
        generators = generators.concat([{
            id: `generator_${generators.length + 1}`,
            generator: "static_token",
            default: false,
            disabled: false,
            parameters: {
                collision_strategy: "FALLBACK"
            },
        }])
        active = generators.length - 1
    }

    function randomItem(array) {
        return array[Math.floor(array.length * Math.random())]
    }

    function randomWord(generator) {
        if(generator.generator == "mixed_groups") {
            let parameters = generator.parameters;
            let word = "";
            let lastGroup = null;
            let consecutive = 1;
            let group = 1;
            for(let i = 0; i < parameters.max_tokens; i++) {
                if(i > parameters.min_tokens && Math.random() < 1/(parameters.max_tokens - parameters.min_tokens + 1))
                    break;
                do{ 
                    group = Math.floor(Math.random() * parameters.groups.length);
                } while(consecutive >= parameters.max_consecutive_groups && group == lastGroup)
                lastGroup = group;
                word += randomItem(parameters.groups[group].tokens)
            }
            return word
        }
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each generators as generator, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {generators[index].name} [{ generators[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addMixedGroupGenerator}>
            + Add Mixed Group Generator
        </button>
        <button class="w-full px-2 odd:bg-gray-100" onclick={addStaticTokenGenerator}>
            + Add Static Token Generator
        </button>
    </div>
    <div class="w-3/4 bg-yellow-50 p-1">
        {#if active < (generators.length)}
            <div class="py-2 flex">
                <div class="px-4">
                    <Disabled bind:disabled={generators[active].disabled} />
                    <span class={generators[active].generator == "static_token" ? "text-gray-500" : ""}>Default:</span>
                    <input type="checkbox" bind:checked="{generators[active].default}" disabled={generators[active].generator == "static_token"}>
                </div>
            </div>
            <div class="py-2 flex">
                <div class="px-4">
                    ID (should be unique)
                    <input type="text" bind:value="{generators[active].id}">
                </div>
                <div class="px-4 w-1/2">
                    {#if generators[active].generator == "mixed_groups" }
                        Example words from this generator:
                        <br>
                        {#each {length: 9}}
                            <b>{ randomWord(generators[active]) }</b>,&nbsp;
                        {/each}
                        <b>{ randomWord(generators[active]) }</b>
                    {/if}
                </div>
            </div>
            <div class="py-2 flex flex-wrap">
                {#if generators[active].generator == "mixed_groups" }
                    {#each generators[active].parameters.groups as group, index}
                        <div class="border m-1 p-2">
                            <div class="p-4">
                                Weight: 
                                <input type="text" bind:value="{generators[active].parameters.groups[index].weight}">
                            </div> 
                            <br>
                            <StringList bind:value={generators[active].parameters.groups[index].tokens} />
                        </div>
                    {/each}
                {:else if generators[active].generator == "static_token" }
                    <div class="p-4">
                        Collision Strategy:
                        <small class="text-sm">This is what happens when two words are configured with the same name.</small> 
                        <select bind:value={generators[active].parameters.collision_strategy}>
                            <option value="FALLBACK">FALLBACK</option>
                            <option value="FAIL">FAIL</option>
                            <option value="SHORTEN">SHORTEN</option>
                        </select>
                    </div> 
                {/if}
            </div>
            <div class="py-2 flex">
                <button class="primary w-full" onclick={addTokenGroup}>Add Token Group</button>
            </div>
        {:else}
            <h2 class="text-xl">Please select or create a generator.</h2>
        {/if}
    </div>
</div>