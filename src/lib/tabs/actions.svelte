<script>
	import Disabled from "$lib/elements/disabled.svelte";
	import StringList from "$lib/elements/string-list.svelte";

    let { actions = $bindable([]) } = $props();
    let active = $state(0);

    function addAction() {
        actions = actions.concat([{
            id: `action_${actions.length + 1}`,
            cost: 1.0,
            disabled: false,
            cast_at_position: [],
            cast_on_entity: [],
            cast_on_item: []
        }])
        active = actions.length - 1
    }
</script>

<div class="flex">
    <div class="w-1/4">
        {#each actions as action, index}
            <button class="w-full px-2 {active == index ? "bg-yellow-100" : "odd:bg-gray-100"}" onclick={() => active = index}>
                [{ actions[index].id }]
            </button>
        {/each}
        <button class="w-full px-2 odd:bg-gray-100" onclick={addAction}>
            + Add New Action
        </button>
    </div>
    <div class="flex-grow-1 bg-yellow-50 p-1">
        {#if active < (actions.length)}
            <Disabled bind:disabled={actions[active].disabled} />
            <div class="py-2 flex">
                <br>
                <div class="px-4">
                    ID (should be unique)
                    <input type="text" bind:value="{actions[active].id}">
                </div>
                <div class="px-4">
                    Cost
                    <input type="number" step="0.1" bind:value="{actions[active].cost}">
                </div>
            </div>
            <div class="py-2">
                <h2 class="text-xl">Cast at Position</h2>
                <p class="text-xs">
                    Commands to run when this spell is cast at a position.
                    <br>
                    Each new line is a separate command.
                    <br>
                    The targeted position is at <b>~ ~ ~</b>.
                    <br>
                    The caster can be referenced using <b>@caster</b>
                    <br>
                    A fake entity located at the targeted position can be referenced using <b>@target</b>
                </p>
                <StringList bind:value={actions[active].cast_at_position} />
            </div>
            <div class="py-2">
                <h2 class="text-xl">Cast on Entity</h2>
                <p class="text-xs">
                    Commands to run when this spell is cast on an entity. 
                    <br>
                    If nothing is specified here, the "Cast at Position" action will be used instead.
                    <br>
                    Each new line is a separate command.
                    <br>
                    The caster can be referenced using <b>@caster</b>
                    <br>
                    A fake entity located at the targeted position can be referenced using <b>@target</b>
                </p>
                <StringList bind:value={actions[active].cast_on_entity} />
            </div>
            <div class="py-2">
                <h2 class="text-xl">Cast on Item</h2>
                <p class="text-xs">
                    Commands to run when this spell is cast on an entity. 
                    <br>
                    If nothing is specified here, the "Cast at Position" action will be used instead.
                    <br>
                    Each new line is a separate command.
                    <br>
                    The caster can be referenced using <b>@caster</b>.
                    <br>
                    A fake entity holding the item in its main hand can be referenced using <b>@s</b>.
                </p>
                <StringList bind:value={actions[active].cast_on_item} />
            </div>
        {:else}
            <h2 class="text-xl">Please select or create an action.</h2>
        {/if}
    </div>
</div>
