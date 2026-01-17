<script>
	import JSZip from "jszip";
    import FileSaver from "file-saver";
	import Actions from "./tabs/actions.svelte";
	import Artifacts from "./tabs/artifacts.svelte";
	import Colors from "./tabs/colors.svelte";
	import Engravings from "./tabs/engravings.svelte";
	import Generators from "./tabs/generators.svelte";
	import Bindings from "./tabs/bindings.svelte";
	import Reagents from "./tabs/reagents.svelte";
	import Scraps from "./tabs/scraps.svelte";
	import Tomes from "./tabs/tomes.svelte";
    import Data from "./data.svelte.js";

    let currentTab = $state("actions");
    let description = $state("A custom data pack for Scriptor Magicae");
    let packFormat = $state(48);
    let defaultResources = $state(true);

    function importPack() {
        // TODO
    }

    function generatePack() {
        const zip = new JSZip();
        const dir = `data/${namespace}/scriptor`;

        zip.file("pack.mcmeta", JSON.stringify({
            pack: {
                description,
                pack_format: packFormat,
                supported_formats: [packFormat, Math.max(packFormat, 57)]
            }
        }));

        function directAdd(item, resourceType) {
            let id = `${item.id}`;
            let clone = JSON.parse(JSON.stringify(item))
            clone.id = undefined;
            zip.file(`${dir}/${resourceType}/${id}.json`, JSON.stringify(clone));
        }

        actions.forEach(i => directAdd(i, "actions"))
        artifacts.forEach(i => directAdd(i, "artifacts"))
        bindings.forEach(i => directAdd(i, "bindings"))
        colors.forEach(i => directAdd(i, "colors"))
        engravings.forEach(i => directAdd(i, "engravings"))
        generators.forEach(i => directAdd(i, "generators"))
        reagents.forEach(i => directAdd(i, "reagents"))
        scraps.forEach(i => directAdd(i, "scraps"))
        tomes.forEach(i => directAdd(i, "tomes"))

        zip.generateAsync({type: "blob"}).then(file => {
            FileSaver.saveAs(file, "custom-scriptor-pack.zip");
        });
    }
</script>

<div class="w-full outline p-2">
    <div class="w-full">
        <button class="tab {currentTab == "actions" ? "active": ""}" onclick={() => currentTab = "actions"}>Actions</button>
        <button class="tab {currentTab == "artifacts" ? "active": ""}" onclick={() => currentTab = "artifacts"}>Artifacts</button>
        <button class="tab {currentTab == "colors" ? "active": ""}" onclick={() => currentTab = "colors"}>Custom Colors</button>
        <button class="tab {currentTab == "engravings" ? "active": ""}" onclick={() => currentTab = "engravings"}>Engravings</button>
        <button class="tab {currentTab == "generators" ? "active": ""}" onclick={() => currentTab = "generators"}>Generators</button>
        <button class="tab {currentTab == "bindings" ? "active": ""}" onclick={() => currentTab = "bindings"}>Generator Bindings</button>
        <button class="tab {currentTab == "reagents" ? "active": ""}" onclick={() => currentTab = "reagents"}>Reagents</button>
        <button class="tab {currentTab == "scraps" ? "active": ""}" onclick={() => currentTab = "scraps"}>Spell Scraps</button>
        <button class="tab {currentTab == "tomes" ? "active": ""}" onclick={() => currentTab = "tomes"}>Spell Tomes</button>
        <button class="tab {currentTab == "settings" ? "active": ""}" onclick={() => currentTab = "settings"}>Other Settings</button>
        <button class="tab {currentTab == "import" ? "active": "primary"}"  onclick={() => currentTab = "import"}>Import</button>
        <button class="tab primary" onclick={generatePack}>Generate</button>
    </div>
    <div class="w-full border px-2 py-4 mb-2 tab-container">
        {#if currentTab == "actions"}
            <Actions bind:actions={Data.actions} />
        {:else if currentTab == "artifacts"}
            <Artifacts bind:artifacts={Data.artifacts} />
        {:else if currentTab == "colors"}
            <Colors bind:colors={Data.colors} />
        {:else if currentTab == "engravings"}
            <Engravings bind:engravings={Data.engravings} />
        {:else if currentTab == "generators"}
            <Generators bind:generators={Data.generators} />
        {:else if currentTab == "bindings"}
            <Bindings bind:bindings={Data.bindings} />
        {:else if currentTab == "reagents"}
            <Reagents bind:reagents={Data.reagents} />
        {:else if currentTab == "scraps"}
            <Scraps bind:scraps={Data.scraps} />
        {:else if currentTab == "tomes"}
            <Tomes bind:tomes={Data.tomes} />
        {:else if currentTab == "import"}
            <div class="text-lg text-red-700">
                Pack importing is not currently implemented. 
                Thank you for your patience!
            </div>
        {:else if currentTab == "settings"}
            <div class="w-full">
                <h1 class="text-xl">Other Settings</h1>
                <p>These are non-resource-specific settings you can change when generating your data pack.</p>
                <hr class="my-2">
                <div>
                    <h2 class="text-l">Namespace</h2>
                    <small class="text-sm">
                        The namespace these resources will be located under.
                        If you don't know what this does, it's usually fine to leave it unchanged.
                    </small>
                    <input type="text" class="w-full" bind:value={Data.namespace}>
                </div>
                <hr class="my-2">
                <div>
                    <h2 class="text-l">Description</h2>
                    <small class="text-sm">
                        An in-game description for this data pack.
                        Purely visual.
                    </small>
                    <input type="text" class="w-full" bind:value={description}>
                </div>
                <hr class="my-2">
                <div>
                    <h2 class="text-l">Pack Format</h2>
                    <small class="text-sm">
                        The format number for this data pack.
                        Used by Minecraft to determine whether this pack is compatible
                        with the current version.
                        <br>
                        For Minecraft version 1.21.1, this should be <b>48</b>.
                    </small>
                    <input type="number" class="w-full" bind:value={packFormat}>
                </div>
                <hr class="my-2">
                <div>
                    <h2 class="text-l">Enable Default Resources</h2>
                    <small class="text-sm">
                        Disable this if you don't want to include references to default actions, colors, reagents, 
                        and generators.
                        <br>
                        Disabling this will still leave builtin actions, descriptors, and subjects which are not
                        loaded via data pack accessible!
                    </small>
                    <div>
                        <input id="defaultResources" type="checkbox" class="" bind:checked={defaultResources}>
                        <label for="defaultResources" class="text-sm">
                            ({defaultResources ? "Default resources will be shown" : "Default resources will not be shown"})
                        </label>
                    </div>
                </div>
            </div>
        {/if}
    </div>
</div>