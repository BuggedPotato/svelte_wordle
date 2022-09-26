<script>
  import PokeCard from "../components/PokeCard.svelte";
  
  let searched = "";
  let raw;
  let data = [];
  assign();
  async function assign()
  {
    raw = await getPokemon();
    data = [...raw];
  }

  async function getPokemon( link = "https://pokeapi.co/api/v2/pokemon?offset=0&limit=100" )
  {
      const res = await fetch( link );
      const data = await res.json();
      
      if( res.ok )
      {
        return data.results;
      }
      else
        throw new Error( data.error );
  }

  function handleInput()
  {
    data = raw.filter( (el)=>{ return el.name.includes( searched ) } );
    // data = data;
    console.log(data)
  }
</script>

<div class="text-gray-200">
    <section class="text-gray-400 bg-gray-900 body-font">
      <input type="text" bind:value={searched} on:input={handleInput}/>
        <div class="container">
          <div class="p-5 flex flex-wrap -m-4">
<!-- 
            {#await data}
                <h1 class="text-4xl tracking-widest animate-pulse">Getting data...</h1>
            {:then data} 
                {#each data as current}
                  {#if foo( current.name ) }
                    <PokeCard url={current.url} />
                  {/if}
                {/each}
            {/await} -->
            {#key data} <!-- ¯\_(ツ)_/¯ -->
              {#each data as poke}
                <PokeCard url={poke.url} />
              {/each}
            {/key}
        </div>
      </section>
</div>
