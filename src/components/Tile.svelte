<script>
    import { savedTheme, themes } from "../assets/themes.js";
    export let id = "";
    export let size = "10%";
    export let colour = "";
    export let icon = null;
    let theme;
    savedTheme.subscribe( v =>{
        theme = themes[v];
    });
</script>

<div on:click class="flex rounded {theme.textMain} {theme.bgMain} {theme.borderMain} border-2 font-bold text-2xl aspect-square justify-center items-center transition-colors delay-150 duration-800" style="flex-basis: {size}; margin: 1px; background-color: {colour}">
    {#if icon}
        <i class="{icon} text-2xl" />
    {:else}
        <span style="{theme.textMain == "text-gray-900" ? "-webkit-text-stroke: 0.5px white;" : ""}">{id}</span>
    {/if}
</div>