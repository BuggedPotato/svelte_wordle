<script>
    let size = { x: 5, y: 5 };
    let length = size.x * size.y;

    import Tile from "../components/Tile.svelte";
</script>

<div class="w-9/12">
    <select bind:value={size.x} on:change="{ ()=> length = size.x * size.y }">
        {#each Array(20) as _, i}
            <option value={i+1}>{i+1}</option>
        {/each}
    </select>
    <select bind:value={size.y} on:change="{ ()=> length = size.x * size.y }">
        {#each Array(20) as _, i}
            <option value={i+1}>{i+1 }</option>
        {/each}
    </select>
    
    <div class="flex flex-wrap">
        {#each Array(length) as _, i}
            <Tile size={(100/size.x)+"%"} id={i} />
        {/each}
    </div>
</div>