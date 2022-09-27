<script>
    import Tile from "./Tile.svelte";
    let template = "qwertyuiop asdfghjkl zxcvbnm";
    let chars = "qwertyuiopasdfghjklzxcvbnm".toUpperCase().split("");
</script>

<div class="w-5/12 h-fit sticky bottom-0 flex flex-col">
    {#each template.split( " " ) as row}
        <div class="flex justify-center">
        {#each row as char}
            <Tile id={char} size="10%" />
        {/each}
        </div>
    {/each}
</div>