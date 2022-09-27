<script>
    import PokeCard from "./PokeCard.svelte";
    export let filteredList;
</script>

{#each filteredList as poke}
    <PokeCard url={poke.url} />
{/each}