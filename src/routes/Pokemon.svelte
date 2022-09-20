<script>
  import PokeCard from "../components/PokeCard.svelte";

    async function getPokemon( link = "https://pokeapi.co/api/v2/pokemon?offset=0" )
    {
        const res = await fetch( link );
        const data = await res.json();
        
        if( res.ok )
        {
          console.log( data )
          return data;
        }
        else
          throw new Error( data.error );
    }
</script>

<div class="text-gray-200">
    <section class="text-gray-400 bg-gray-900 body-font">
        <div class="container">
          <div class="p-5 flex flex-wrap -m-4">

            {#await getPokemon()}
                <h1 class="text-4xl tracking-widest animate-pulse">Getting data...</h1>
            {:then data} 
                {#each data.results as current}
                    <PokeCard url={current.url} />
                {/each}
            {/await}
            
        </div>
      </section>
</div>
