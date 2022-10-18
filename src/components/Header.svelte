<script>
  import '../lib/Tailwind.css'
  import { Router, Link, Route } from "svelte-routing";
  import Home from '../routes/Home.svelte';
  import About from '../routes/About.svelte';
  import Board from '../routes/Board.svelte';
  import Pokemon from '../routes/Pokemon.svelte';
  import Wordle from '../routes/Wordle.svelte';

  import { savedTheme, themes, setTheme } from "../assets/themes.js";

  export let url = "";
  let theme;
  savedTheme.subscribe( v =>{
    console.log( v )
    window.document.body.classList.remove( ...Object.values( themes ).map( t => t.bgMain ) )
    theme = themes[v];
    window.document.body.classList.add( theme.bgMain );
  });

</script>


<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.9.1/font/bootstrap-icons.css"/>
<Router url={url}>
  <header class="{theme.textMain} body-font after:flex after:mx-12 after:{theme.borderMain} after:h-[0.1rem] rounded-lg">
    <div class="container mx-auto flex flex-wrap p-5 flex-col md:flex-row items-center">
      <img class="invert" src="https://upload.wikimedia.org/wikipedia/commons/e/e5/NASA_logo.svg" />
      <span class="ml-3 text-xl">!NASA</span>
      <nav class="md:mr-auto md:ml-4 md:py-1 md:pl-4 md:border-l md:border-gray-400	flex flex-wrap items-center text-base justify-center">
        <Link to="" class="mr-5 hover:text-gray-500">Home</Link>
        <Link to="wordle" class="mr-5 hover:text-gray-500">Wordle</Link>
        <Link to="about" class="mr-5 hover:text-gray-500">About</Link>
        <Link to="board" class="mr-5 hover:text-gray-500">Board</Link>
        <Link to="pokemon" class="mr-5 hover:text-gray-500">Pokemon</Link>
      </nav>
      <div>
        <i class="bi-moon-stars" style="font-size: 1.5rem" on:click={()=>setTheme( "dark" )}/>
        <i class="bi-sun-fill mx-3" style="font-size: 1.5rem" on:click={()=>setTheme( "light" )} />
        <i class="bi-bootstrap-fill" style="font-size: 1.5rem" on:click={()=>setTheme( "contrast" )}/>
      </div>
    </div>
  </header>
  <main class="flex justify-center w-full">
    <Route path="/" component={Home} />
    <Route path="wordle" component={Wordle} />
    <Route path="about" component={About} />
    <Route path="board" component={Board} />
    <Route path="pokemon" component={Pokemon} />
  </main>
</Router>
