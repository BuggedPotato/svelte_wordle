<script>
    import { createEventDispatcher } from "svelte";
    import Tile from "./Tile.svelte";
    import { debugCode } from "../assets/debug";

    let template = "qwertyuiop asdfghjkl ^zxcvbnm<".toUpperCase();
    $: tiles = template.split( "" ).map( ( char, i )=>{
        let c = colours.default;
        if( usedChars.guessed.includes(char) )
            c = colours.correct;
        else if( usedChars.missed.includes(char) )
            c = colours.miss;
        else if( usedChars.ded.includes(char) )
            c = colours.ded;
        return { char: char, colour: c };
    } );
    const chars = "qweęrtyuioópaąsśdfghjklłzżźxcćvbnńm".toUpperCase().split("");
    const dispatch = createEventDispatcher();
    
    export let guess;
    export let wordLength;
    export let colours;
    export let usedChars;
    export let go;

    const handleInput = ( key ) => {
        if( debugCode.length > 0 && key == debugCode[0] ){
            debugCode.shift();
            if( debugCode.length == 0 )
                dispatch( "debugOn" );
        }
        if( !go ) return;
        if( key == "ENTER" )
        dispatch( "submitGuess", {
            guess: guess
        } );
        else if( key == "<=" || key == "BACKSPACE" )
        guess = guess.slice( 0, -1 );
        else if( chars.includes( key ) && guess.length + 1 <= wordLength ) {
            guess += key;
        }
    }

    document.addEventListener( "keydown", (e)=> handleInput( e.key.toUpperCase() ) );
</script>

<div class="w-5/12 h-fit sticky bottom-0 flex flex-row flex-wrap justify-center">
    {#each tiles as tile}
        {#if tile.char == " "}
            <span></span>
        {:else if tile.char == '^'}
            <Tile on:click={(e)=> handleInput("ENTER") } id="ENTER" size="10%" colour={tile.colour} />
        {:else if tile.char == '<'}
            <Tile on:click={(e)=> handleInput("<=") } id="<=" icon="bi-backspace" size="10%" colour={tile.colour} />
        {:else}
            <Tile on:click={(e)=> handleInput(tile.char) } id={tile.char} size="9%" colour={tile.colour} />
        {/if}
    {/each}
</div>