<script>
    import { fade, fly } from "svelte/transition";

    export let text;
    export let buttons = [];
</script>

<div class="h-full absolute">
    <div in:fly="{{ y: -60, duration: 500 }}" out:fade class="rounded-lg sticky z-30 top-8 bg-black text-gray-200 border-dashed border-yellow-500 border-4 p-6">
        <h1 class="text-2xl">{text}</h1>
        {#if buttons.length > 0}
            <div class="flex justify-evenly border-t-2 border-gray-800">
                {#each buttons as btn}
                    <button type="button" class="bg-{btn.background} rounded-md p-2 m-4 text-xl" on:click={btn.clickHandler}>{btn.label}</button>
                {/each}
            </div>
        {/if}
    </div>
</div>