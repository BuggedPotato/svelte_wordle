<script>
    import Tile from "../components/Tile.svelte";
    import Keyboard from "../components/Keyboard.svelte";
    // import {wordsList} from "random-words";
    

    let wordLength = 5;
    const tries = 6;
    let length = wordLength * tries;

    var raw = [];
    var data = [];
    let word = init();
    let go = false;

    async function init()
    {
        raw = await getWords();
        return newWord( wordLength );
    }

    async function getWords()
    {
        let res = await fetch( "https://random-word-api.herokuapp.com/all" );
        return (await res.json()).filter( word => word.length <= 9 && word.length >= 4 );
    }

    function newWord( length )
    {
        data = raw.filter( word => word.length == length );
        if( data.length == 0 )
            return;
        console.log(data.length)
        let i = Math.round(Math.random() * data.length);
        console.log("i: ", i)
        while( data[ i ] == undefined )
        {
            console.log("i: ", i)
            i = Math.round(Math.random() * data.length);
        }
        go = true;
        return data[i];
    }

    function handleChange()
    {
        length = wordLength * tries;
        word = newWord(wordLength);
        console.log(word);
    }
</script>

<div class="flex flex-col w-full h-screen items-center">
    <div class="w-4/12 h-full mb-16">
        <label class="text-gray-200" for="wordLength">Word length:</label>
        <select id="wordLength" bind:value={wordLength} on:change="{ handleChange }" disabled={!go}>
            {#each Array(6) as _, i}
                <option value={i+4}>{i+4}</option>
            {/each}
        </select>
        {#await word then val}
            <h1 class="text-gray-200">{val}</h1>
        {/await}
        <div class="flex flex-wrap h-full">
            {#each Array(length) as _, i}
                <Tile size={100/wordLength - 1 + "%"} id={i} /> <!-- ¯\_(ツ)_/¯ -->
            {/each}
        </div>
    </div>
    <Keyboard />
</div>