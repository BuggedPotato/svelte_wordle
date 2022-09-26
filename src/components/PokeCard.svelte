<script>
    export let url = "";

    async function getPokemonData()
    {
        const res = await fetch( url );
        const data = await res.json();
        
        if( res.ok )
        {
          return data;
        }
        else
          throw new Error( data.error );
    }

    function toTitleCase( string )
    {
        return string[0].toUpperCase() + string.substr(1);
    }
</script>

<div class="md:w-1/2 bg-yellow-500 rounded" style="width: calc(33% - 20px); margin: 10px;">
    <div class="h-full flex flex-col items-center text-center">
        {#await getPokemonData()}
          <p>dadada</p>
        {:then data} 
          <img src="{data.sprites.front_default}" class="w-full h-full" style="image-rendering: pixelated;" />
          <span class="bg-yellow-400 w-full rounded-b">
              <div class="w-full">
                  <h2 class="title-font font-medium text-lg text-white">{toTitleCase(data.name)}</h2>
                  <h3 class="text-gray-500 mb-3">{data.types[0].type.name} type</h3>
                </div>
          </span>
        {/await}
    </div>
</div>