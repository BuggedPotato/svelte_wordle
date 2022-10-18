<script>
    import { fade, fly } from "svelte/transition";

    export let text;
    let ref;
</script>

<div in:fly="{{ y: -60, duration: 500 }}" out:fade bind:this={ref} class="rounded-lg absolute top-8 bg-gray-700 text-gray-200 border-dashed border-yellow-500 border-4 p-6">
    <h1>{text}</h1>
</div>