<script>
    export let src, title, type, text, author = null, date;

    const getDateString = ( origDate )=> {
        return origDate.split('-').reverse().join('.');
    }
</script>

<div class="p-4 md:w-1/3 sm:mb-0 mb-6">
    <div class="rounded-lg h-64 overflow-hidden">
        {#if type == "video"}
            <iframe width="100%" height="100%" src={src} ></iframe> 
        {:else}
            <img alt="content" class="object-cover object-center h-full w-full" src={src}>
        {/if}
    </div>
    <h2 class="text-xl font-medium title-font text-white mt-5">{title}</h2>
    <h3 class="italic text-gray-500 my-2">Date: {getDateString(date)}</h3>
    <p class="text-base leading-relaxed text-gray-400">{text}</p>
    {#if author != null}
        <div class="flex justify-end">
            <p class="text-yellow-500 italic text-lg mt-3">
                Credit: {author}
            </p>
        </div>
    {/if}
</div>
