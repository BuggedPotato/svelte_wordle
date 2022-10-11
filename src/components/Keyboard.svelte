<script>
    import { createEventDispatcher } from "svelte";
    import Tile from "./Tile.svelte";
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

    const handleInput = ( key ) => {
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
    <!-- {#each template.split( " " ) as row, i}
        <div class="flex justify-center">
        {#each row as char}
            {#if char == '^'}
                <Tile on:click={(e)=> handleInput("ENTER") } id="ENTER" size="10%" />
            {:else if char == '<'}
                <Tile on:click={(e)=> handleInput("<=") } id="<=" size="10%" />
            {:else}
                <Tile on:click={(e)=> handleInput(char) } id={char} size="10%" colour={tiles[i].colour} />
            {/if}
        {/each}
        </div>
    {/each} -->
    {#each tiles as tile}
        {#if tile.char == " "}
            <span></span>
        {:else if tile.char == '^'}
            <Tile on:click={(e)=> handleInput("ENTER") } id="ENTER" size="10%" />
        {:else if tile.char == '<'}
            <Tile on:click={(e)=> handleInput("<=") } id="<=" size="10%" />
        {:else}
            <Tile on:click={(e)=> handleInput(tile.char) } id={tile.char} size="9%" colour={tile.colour} />
        {/if}
    {/each}
</div>