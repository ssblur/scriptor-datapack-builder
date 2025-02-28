<script>
    import App from "$lib/app.svelte";
    const scriptorVersion = "v1.4"
</script>

<div class="mt-4">
    <div class="justify-center flex">
        <h1 class="text-xl">Scriptor Datapack Builder</h1>
    </div>
    <div class="justify-center flex">
        <div class="text-sm mx-1">You can use this tool to create custom datapacks for Scriptor Magicae.</div>
        <div class="text-sm mx-1">This was built with Scriptor version <b>{ scriptorVersion }</b> in mind!</div>
    </div>
    <br />
    <App />
</div>