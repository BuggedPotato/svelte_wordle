<script>
    import { createEventDispatcher } from "svelte";
    import Tile from "./Tile.svelte";
    let template = "qwertyuiop asdfghjkl ^zxcvbnm<".toUpperCase();
    let chars = "qweęrtyuioópaąsśdfghjklłzżźxcćvbnńm".toUpperCase().split("");

    export let guess;
    export let wordLength;
    const dispatch = createEventDispatcher();

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

<div class="w-5/12 h-fit sticky bottom-0 flex flex-col">
    {#each template.split( " " ) as row}
        <div class="flex justify-center">
        {#each row as char}
            {#if char == '^'}
                <Tile on:click={(e)=> handleInput("ENTER") } id="ENTER" size="10%" />
            {:else if char == '<'}
                <Tile on:click={(e)=> handleInput("<=") } id="<=" size="10%" />
            {:else}
                <Tile on:click={(e)=> handleInput(char) } id={char} size="10%" />
            {/if}
        {/each}
        </div>
    {/each}
</div>