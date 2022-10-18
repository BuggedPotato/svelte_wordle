<script>
    import Tile from "../components/Tile.svelte";
    import Keyboard from "../components/Keyboard.svelte";
    import Alert from "../components/Alert.svelte";
    import { themes, savedTheme } from "../assets/themes";
    
    let theme;
    savedTheme.subscribe( v =>{
        theme = themes[v];
    });

    $: colours = {
        default: theme.bgMainHexadec,
        correct: "#32b015",
        miss: "#fad905",
        ded: "#0d0d0d"
    }
    const endGameButtons = [
        {
            background: "yellow-400",
            label: "Try again!",
            clickHandler: () => {
                alert.show = false;
                handleChange();
            }
        },
        {
            background: "red-500",
            label: "Close",
            clickHandler: () => alert.show = false
        }
    ];

    let debug = false;

    let wordLength = 5;
    const availableTries = 6;
    let hinted = false;
    let length = wordLength * availableTries;
    let currentTry = 0;

    let alert = {
        show: false,
        text: "I am an Alert please dont hurt me :(",
        buttons: [],
        timeout: null
    };

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
    $: globProps = Array(length).fill(undefined).map((_,i) => getTile(tries[Math.floor(i / wordLength)].text[(i + wordLength) % wordLength], tries[Math.floor(i / wordLength)].guess[(i + wordLength) % wordLength] ));
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
        return (await res.json()).filter( word => word.length <= 9 && word.length >= 4 ).map( w => w.toUpperCase() );
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
        word = data[i];
    }

    function handleChange()
    {
        length = wordLength * availableTries;
        newWord(wordLength);
        for( let i = 0; i < availableTries; i++ )
            tries[i] = {text: "", guess: []};
        currentTry = 0;
        hinted = false;
    }

    function handleSubmit( _, force = false )
    {
        const guess = tries[currentTry].text;
        if( guess.length < wordLength ){
            showAlert( "The word is too short", true );
            return;
        }
        if( !force && !data.includes( guess ) )
        {
            showAlert( "Word not found", true )
            return
        }
        if( currentTry <= availableTries - 1 )
        {
            checkGuess( tries[currentTry].text );
            if( currentTry < availableTries - 1 )
                currentTry++;
        }
    }

    const checkGuess = ( string ) => {
        let correct = true;
        for( let i = 0; i < wordLength; i++ ){
            if( word[i] == string[i] ){
                tries[currentTry].guess[i] = 1;
                usedChars.guessed += string[i];
            }
            else if( word.toString().includes( string[i] ) ){
                tries[currentTry].guess[i] = 0;
                usedChars.missed += string[i];
                correct = false;
            }
            else{
                tries[currentTry].guess[i] = -1;
                usedChars.ded += string[i];
                correct = false;
            }
        }
        if( correct ){
            go = false;
            showAlert( `Congratulations! You guessed the word - ${word}. It took you ${currentTry + 1} tries.`, false, endGameButtons );
        }
        else if( currentTry >= availableTries - 1 ){
            go = false;
            showAlert( `Oh no! You ran out of tries. The word was ${word}.`, false, endGameButtons );
        }
    }

    const getTile = ( sourceChar, sourceColour ) => {
        const size = 100/wordLength - 1 + "%";
        const char = sourceChar ? sourceChar : "";
        let colour = "transparent";
        if( sourceColour == 1 )
            colour = colours.correct;
        else if( sourceColour == 0 )
            colour = colours.miss;
        else if( sourceColour == -1 )
            colour = colours.ded;
        const props = {
            id: char, size: size, colour: colour
        };
        console.log( props )
        return props;
    }

    const hint = () => {
        if( hinted ) 
            return;
        hinted = true;
        const n = Math.floor(word.length / 2);
        
        for( let k = currentTry; k < availableTries - 2; k++ ){
            tries[k].guess = [...Array(wordLength).fill( -1 )];
        }

        currentTry = availableTries - 2;
        let str = Array( word.length ).fill( "_" );
        for( let i = 0; i < n; i++ ){
            const index = Math.round( Math.random() * word.length );
            if( str[index] != "_" ){
                i--;
                continue;
            }
            str[index] = word[index];
        }
        tries[currentTry].text = str.join( "" );
        handleSubmit( null, true );
    }

    const showAlert = ( text, disappear, buttons = [] ) => {
        alert.show = true;
        alert.text = text;
        alert.buttons = [...buttons];
        if( disappear ){
            if( alert.timeout == null ){
                alert.timeout = setTimeout( ()=> alert.show = false, 2000 );
            }else{
                clearTimeout( alert.timeout );
                alert.timeout = setTimeout( ()=> alert.show = false, 2000 );
            }
        }
        else if( !disappear && alert.timeout != null ){
            clearTimeout( alert.timeout );
            alert.timeout = null;
        }
    }

    const parseJson = async ( file ) => {
        return new Promise((resolve, reject) => {
            const fileReader = new FileReader();
            fileReader.onload = event => resolve( JSON.parse( event.target.result.toString() ) );
            fileReader.onerror = error => reject( error );
            fileReader.readAsText( file );
        })
    }

    const loadFile = async ( event ) => {
        const read = await parseJson( event.target.files[0] );
        console.log(read)
        try{
            raw = [ ...read.map( w => w.toUpperCase() ) ];
        }catch( TypeError ){
            showAlert( "Invalid JSON format", true );
        }
        handleChange();
    }

    const debugOn = () => {
        showAlert( "DEBUG MODE ON", true );
        debug = true;
    }
</script>

<div class="flex flex-col w-screen h-screen items-center">
    {#if alert.show}
        <Alert text={alert.text} buttons={alert.buttons} />
    {/if}
    <div class="w-1/2 mb-16">
        <div class="flex justify-evenly items-center m-6 text-lg">
            <div>
                <label class="{theme.textMain}" for="wordLength">Word length:</label>
                <select id="wordLength" class="p-1 border-2 rounded-sm { !go ? "bg-slate-900 text-gray-600 border-slate-800" : "bg-slate-700 text-gray-200 border-slate-500"}" bind:value={wordLength} on:change="{ handleChange }" disabled={!go}>
                    {#each Array(6) as _, i}
                        <option value={i+4}>{i+4}</option>
                    {/each}
                </select>
            </div>
            <div class="flex flex-col">
                <label for="file-input" class="text-md inline-block cursor-pointer p-2 rounded bg-slate-700 text-gray-200 border-slate-500 border-2">
                    <i class="bi-cloud-upload-fill text-xl mr-1" />
                    Load custom JSON
                </label>
                <input type="file" id="file-input" style="display: none;" class="{theme.textMain} text-sm {theme.bgMain} block" accept=".json" on:change={loadFile} />
            </div>
            <button type="button" class="{hinted || !go ? "bg-slate-900 text-gray-600 border-slate-800" : "bg-slate-700 text-gray-200 border-slate-500"} p-2 px-6 transition rounded border-4" disabled={!go} on:click={hint}>HINT</button>
            {#if debug}
                {#await word then val}
                    <h1 class="text-amber-500">{val}</h1>
                {/await}
            {/if}
        </div>

        <div class="flex flex-wrap h-full">
            {#each globProps as props }
                <Tile id={props.id} colour={props.colour} size={props.size} />
            {/each}
        </div>

    </div>
    <Keyboard bind:usedChars={usedChars} colours={colours} bind:go={go} bind:guess={tries[currentTry].text} bind:wordLength={wordLength} on:submitGuess={handleSubmit} on:debugOn={debugOn} />
</div>