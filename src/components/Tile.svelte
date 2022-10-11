<script>
    export let id = "";
    export let size = "10%";
    export let colour = '#101726';
</script>

<div on:click class="flex rounded-sm text-gray-100 text-2xl aspect-square justify-center items-center transition-colors delay-150 duration-800" style="flex-basis: {size}; margin: 1px; background-color: {colour}; border: 2px solid #21283b;">
    {id}
</div>