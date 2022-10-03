<script>
    export let size = "10%";
    export let id;
</script>

<div on:click class="flex rounded-sm text-gray-100 text-2xl aspect-square justify-center items-center" style="flex-basis: {size}; margin: 1px; background-color: #101726; border: 2px solid #21283b;">
    {id}
</div>