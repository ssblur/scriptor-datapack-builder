<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import ItemSearch from "$lib/elements/item-search.svelte";
    import Data from "$lib/data.svelte.js";

    let {
        generators,
        actions,
        namespace
    } = Data

    let { 
        bindings = $bindable([]),
    } = $props();
    let active = $state(0);
    let currentWord = $state("");

    function addBinding() {
        bindings = bindings.concat([{
            id: `binding_${bindings.length + 1}`,
            disabled: false,
            generator: "",
            bindings: [],
        }])
        active = bindings.length - 1
    }

    function generatorFor(binding) {
        return generators.find(item => item.id == binding.generator || `${namespace}:${item.id}` == binding.generator)
    }

    function addWordBinding() {
        let activeGenerator = generatorFor(bindings[active])
        if(!activeGenerator) return;
        let activeBindings = bindings[active].bindings
        if(activeGenerator.generator == "mixed_groups") {
            activeBindings = activeBindings.concat([{
                word: currentWord,
                parameters: {
                    min_tokens: 3,
                    max_tokens: 5,
                },
            }]);
        } else if(activeGenerator.generator == "static_token") {
            activeBindings = activeBindings.concat([{
                word: currentWord,
                parameters: {
                    token: currentWord,
                },
            }]);
        }
        bindings[active] = {
            ...bindings[active],
            bindings: activeBindings
        };
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each bindings as binding, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {bindings[index].name} [{ bindings[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addBinding}>
            + Add New Binding Group
        </button>
    </div>
    <div class="w-3/4 bg-yellow-50 p-1">
        {#if active < (bindings.length)}
            <div class="py-2 flex">
                <Disabled bind:disabled={bindings[active].disabled} />
            </div>
            <div class="py-2">
                <p>Generator to use: </p>
                <ItemSearch 
                    bind:item={bindings[active].generator} 
                    items={
                        generators.map(item => item.id.includes(":") ? item.id : `${namespace}:${item.id}`)
                    } />
            </div>
            <div class="py-2">
                <hr>
                <p>Bindings: </p>
                {#if bindings[active].bindings.length == 0}
                    <div class="w-full odd:bg-gray-200 py-2 px-4 relative flex items-center">
                        No entries added.
                    </div>
                {/if}
                {#each bindings[active].bindings as binding, index}
                    <div class="w-full odd:bg-gray-200 py-2 px-4 relative flex items-center">
                        <div class="px-4 w-1/4">Word: {binding.word}</div>
                        <div class="px-4 flex-grow">
                            <p>Parameters:</p>
                            <div class="p-2">
                                {#if generatorFor(bindings[active]).generator == "static_token"}
                                    <div class="flex w-full">
                                        <div class="pr-5">
                                            Token:
                                            <p class="text-sm">This word will always generate as this</p>
                                        </div>
                                        
                                        <input type="text" class="flex-grow" bind:value={bindings[active].bindings[index].parameters.token}>
                                    </div>
                                {:else if generatorFor(bindings[active]).generator == "mixed_groups"}
                                    <div class="flex w-full pb-4">
                                        <div class="pr-5 w-1/2">
                                            Minimum Tokens:
                                            <p class="text-sm">The least number of tokens that can be combined into this word</p>
                                        </div>
                                        
                                        <input type="number" class="flex-grow" bind:value={bindings[active].bindings[index].parameters.min_tokens}>
                                    </div>
                                    <div class="flex w-full">
                                        <div class="pr-5 w-1/2">
                                            Maximum Tokens:
                                            <p class="text-sm">The greatest number of tokens that can be combined into this word</p>
                                        </div>
                                        
                                        <input type="number" class="flex-grow" bind:value={bindings[active].bindings[index].parameters.max_tokens}>
                                    </div>
                                {/if}
                            </div>
                        </div>
                    </div>
                {/each}
            </div>
            <div class="py-2">
                <hr>
                <ItemSearch 
                    bind:item={currentWord} 
                    items={
                        actions.map(item => item.id.includes(":") ? `action.${item.id.replace(":", ".")}` : `${namespace}:action.${namespace}.${item.id}`)
                    } />
                <button 
                    disabled={!generators.some(item => item.id == bindings[active].generator || `${namespace}:${item.id}` == bindings[active].generator)} 
                    class="primary m-2 w-full" 
                    onclick={addWordBinding}>
                    Add Word Binding
                </button>
            </div>
        {:else}
            <h2 class="text-xl">Please select or create a binding.</h2>
        {/if}
    </div>
</div>