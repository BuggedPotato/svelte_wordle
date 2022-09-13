<script>
  import APOD from "../components/APOD.svelte";

    async function getAPOD()
    {
        const apiKey = "uX61CGXk4HhFId5ukzW1qWop9km9Shy8JupBVm4e";
        const res = await fetch( "https://api.nasa.gov/planetary/apod?api_key=" + apiKey + "&count=3" );
        const data = await res.json();
        
        if( res.ok )
        {
          console.log( data )
          return data;
        }
        else
          throw new Error( data.error );
    }
    
    let promise = getAPOD();
</script>

<section class="text-gray-600 body-font">
  <div class="container px-5 mx-auto">
    <div class="flex flex-col">
      <div class="flex flex-wrap sm:flex-row flex-col py-6 mb-12 items-center">
        <h1 class="sm:w-2/5 text-gray-50 font-medium title-font text-6xl sm:mb-0">Astronomy Picture of the Day</h1>
        <p class="sm:w-3/5 text-gray-300 leading-relaxed text-xl sm:pl-10 pl-0">Discover the cosmos! Each day a different image or photograph of our fascinating universe is featured, along with a brief explanation written by a professional astronomer.</p>
      </div>
      <div class="flex h-1 bg-gray-800 rounded overflow-hidden my-5">
        <div class="w-1/3 h-full bg-yellow-500"></div>
        <div class="w-1/3 h-full bg-orange-500"></div>
        <div class="w-1/3 h-full bg-red-500"></div>
      </div>
    </div>

    <div class="flex flex-wrap justify-center">
      
      {#await promise}
          <h1 class="text-4xl tracking-widest animate-pulse">Getting data...</h1>
      {:then data}
        {#each data as apod}
          <APOD title={apod.title} text={apod.explanation} type={apod.media_type} src={apod.url} date={apod.date} author={apod.copyright} />
        {/each}
      {:catch err}
          <h2 style="color: red;">{err.message}</h2>
      {/await}

    </div>
  </div>
</section>