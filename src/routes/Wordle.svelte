<script>
    import Tile from "../components/Tile.svelte";
    import Keyboard from "../components/Keyboard.svelte";

    let wordLength = 5;
    const availableTries = 6;
    let length = wordLength * availableTries;
    let guess = "";
    let currentTry = 0;
    let tries = Array(availableTries);
    for( let i = 0; i < availableTries; i++ )
        tries[i] = {text: "", guess: []};

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
        length = wordLength * availableTries;
        word = newWord(wordLength);
        for( let i = 0; i < availableTries; i++ )
            tries[i] = {text: "", guess: []};
        currentTry = 0;
    }

    function handleSubmit( e )
    {
        if( tries[currentTry].text.length < wordLength ){
            alert( "The word is too short" );
            return;
        }
        if( currentTry < availableTries - 1 )
        {
            checkGuess( tries[currentTry].text );
            currentTry++;
            console.log( tries );
        }
    }

    const checkGuess = ( string ) => {
        for( let i = 0; i < wordLength; i++ ){
            if( word[i] == string[i] )
                tries[currentTry].guess[i] = 1;
            else if( word.toString().includes( string[i] ) )
                tries[currentTry].guess[i] = 0;
            else
                tries[currentTry].guess[i] = -1;
        }
    }

    const getTile = ( i ) => {
        const size = 100/wordLength - 1 + "%";
        const sourceChar = tries[Math.floor(i / wordLength)].text[(i + wordLength) % wordLength];
        const char = sourceChar ? sourceChar : "";
        const sourceColour = tries[Math.floor(i / wordLength)].guess[(i + wordLength) % wordLength];
        let colour = '#101726';
        if( sourceColour == 1 )
            colour = "green";
        else if( sourceColour == 0 )
            colour = "yellow";
        return <Tile id={char} size={size} colour={colour} />
    }
</script>

<div class="flex flex-col w-screen h-screen items-center">
    <div class="w-1/2 mb-16">
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
                getTile( i );
            {/each}
        </div>

    </div>
    <Keyboard bind:guess={tries[currentTry].text} bind:wordLength={wordLength} on:submitGuess={handleSubmit} />
</div>