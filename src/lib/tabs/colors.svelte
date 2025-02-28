<script>
	import Disabled from "$lib/elements/disabled.svelte";

    let { colors = $bindable([]) } = $props();
    let active = $state(0);
    let currentColor = $state("#ff0000");

    function addColor() {
        colors = colors.concat([{
            id: `color_${colors.length + 1}`,
            disabled: false,
            color: []
        }])
        active = colors.length - 1
    }

    function addColorToActive() {
        colors[active].color = colors[active].color.concat([currentColor.replace("#", "").trim()])
    }

    function removeFromColors(index) {
        colors[active].color = colors[active].color.filter((_, i) => i != index)
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each colors as color, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                {colors[index].name} [{ colors[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addColor}>
            + Add New Color Word
        </button>
    </div>
    <div class="flex-grow-1 bg-yellow-50 p-1">
        {#if active < (colors.length)}
            <Disabled bind:disabled={colors[active].disabled} />
                
            <div class="py-2">
                <div class="px-4">
                    Colors: 
                    <p class="text-sm">Spells with this descriptor will cycle through the colors listed, in order.</p>
                    <br>
                    <div class="mx-2 w-full bg-gray-50">
                        {#if colors[active].color.length === 0}
                            <div class="w-full bg-gray-200 py-2 px-4 relative">
                                No colors have been added. Add a color using the controls below.
                            </div>
                        {/if}
                        {#each colors[active].color as color, index}
                            <div class="w-full odd:bg-gray-200 py-2 px-4 relative flex items-center">
                                <div class="rounded-full swatch" style="background-color:#{color};"></div>
                                <p class="mx-6">#{color}</p>
                                <button class="button-sm danger" onclick={() => removeFromColors(index)}>Remove</button>
                            </div>
                        {/each}
                    </div>
                    <br>
                    <div class="p-2 flex w-1/2 items-center">
                        <div class="m-2">
                            <input class="" type="color" bind:value={currentColor}>
                        </div>
                        <div class="m-2">
                            <button class="primary" onclick={addColorToActive}>Add Color</button>
                        </div>
                    </div>
                </div>
            </div>
        {:else}
            <h2 class="text-xl">Please select or create a custom color.</h2>
        {/if}
    </div>
</div>