<script>
    import Tile from "../components/Tile.svelte";
    // import {wordsList} from "random-words";
    

    let wordLength = 5;
    const tries = 6;
    let length = wordLength * tries;

    var raw = [];
    var data = [];
    let word = init();

    async function init()
    {
        raw = await getWords();
        data = raw.filter( word => word.length == length );
        return newWord( wordLength );
    }

    async function getWords()
    {
        let res = await fetch( "https://random-word-api.herokuapp.com/all" );
        return await res.json();
    }

    function newWord( length )
    {
        data = raw.filter( word => word.length == length );
        return data[ Math.round(Math.random() * data.length) ];
    }
</script>

<div class="w-fit h-fit">
    <label class="text-gray-200" for="wordLength">Word length:</label>
    <select id="wordLength" bind:value={wordLength} on:change="{ ()=> { length = wordLength * tries; word = newWord(wordLength); console.log(word) } }">
        {#each Array(6) as _, i}
            <option value={i+4}>{i+4}</option>
        {/each}
    </select>
    {#await word then val}
        <h1 class="text-gray-200">{val}</h1>
    {/await}
    <div class="flex flex-wrap h-full">
        {#each Array(length) as _, i}
            <Tile size={(100/wordLength)+"%"} id={i} />
        {/each}
    </div>
</div>