<script>
    import Tile from "../components/Tile.svelte";
    import Keyboard from "../components/Keyboard.svelte";

    let wordLength = 5;
    const availableTries = 6;
    let length = wordLength * availableTries;
    let currentTry = 0;
    const colours = {
        default: "#101726",
        correct: "#32b015",
        miss: "#fad905",
        ded: "black"
    }

    let usedChars = {
        guessed: "",
        missed: "",
        ded: ""
    }

    let tries = Array(availableTries);
    for( let i = 0; i < availableTries; i++ )
        tries[i] = {text: "", guess: []};

    var raw = [];
    var data = [];
    $: globProps = Array(length).fill(undefined).map((_,i)=>getTile(tries[Math.floor(i / wordLength)].text[(i + wordLength) % wordLength], tries[Math.floor(i / wordLength)].guess[(i + wordLength) % wordLength] ));
    let word = "";
    init();
    let go = false;

    async function init()
    {
        raw = await getWords();
        newWord( wordLength );
    }

    async function getWords()
    {
        let res = await fetch( "https://random-word-api.herokuapp.com/all" );
        return (await res.json()).filter( word => word.length <= 9 && word.length >= 4 );
    }

    function newWord( length )
    {
        usedChars.guessed = "";
        usedChars.missed = "";
        usedChars.ded = "";
        data = raw.filter( word => word.length == length );
        if( data.length == 0 )
            return null;
        console.log(data.length)
        let i = Math.round(Math.random() * data.length);
        console.log("i: ", i)
        while( data[ i ] == undefined )
        {
            console.log("i: ", i)
            i = Math.round(Math.random() * data.length);
        }
        go = true;
        word = data[i].toUpperCase();
    }

    function handleChange()
    {
        length = wordLength * availableTries;
        newWord(wordLength);
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
        }
    }

    const checkGuess = ( string ) => {
        for( let i = 0; i < wordLength; i++ ){
            if( word[i] == string[i] ){
                tries[currentTry].guess[i] = 1;
                usedChars.guessed += string[i];
            }
            else if( word.toString().includes( string[i] ) ){
                tries[currentTry].guess[i] = 0;
                usedChars.missed += string[i];
            }
            else{
                tries[currentTry].guess[i] = -1;
                usedChars.ded += string[i];
            }
        }
    }

    const getTile = ( sourceChar, sourceColour ) => {
        const size = 100/wordLength - 1 + "%";
        const char = sourceChar ? sourceChar : "";
        let colour = colours.default;
        if( sourceColour == 1 )
            colour = colours.correct;
        else if( sourceColour == 0 )
            colour = colours.miss;
        else if( sourceColour == -1 )
            colour = colours.ded;
        const props = {
            id: char, size: size, colour: colour
        };
        return props;
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
            {#each globProps as props }
                <Tile id={props.id} colour={props.colour} size={props.size} />
            {/each}
        </div>

    </div>
    <Keyboard bind:usedChars={usedChars} colours={colours} bind:guess={tries[currentTry].text} bind:wordLength={wordLength} on:submitGuess={handleSubmit} />
</div>