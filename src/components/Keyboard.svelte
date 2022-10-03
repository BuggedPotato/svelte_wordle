<script>
    import { createEventDispatcher } from "svelte";
    import Tile from "./Tile.svelte";
    let template = "qwertyuiop asdfghjkl ^zxcvbnm<".toUpperCase();
    let chars = "qwertyuiopasdfghjklzxcvbnm".toUpperCase().split("");

    export let guess;
    export let wordLength;
    const dispatch = createEventDispatcher();

    const handleInput = ( key ) =>{
        if( key == "ENTER" )
            dispatch( "submitGuess", {
                guess: guess
            } );
        else if( key == "<=" )
            guess = guess.slice( 0, -1 );
        else {
            if( guess.length + 1 > wordLength )
                return;
            guess += key;
        }
    }
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