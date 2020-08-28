<script>
  import Repl from "@sveltejs/svelte-repl";
  import { onMount } from "svelte";

  export let name = "";
  let repl;

  function process_example(files) {
    return files
      .map((file) => {
        const [name, type] = file.name.split(".");
        return { name, type, source: file.source };
      })
      .sort((a, b) => {
        if (a.name === "App" && a.type === "svelte") return -1;
        if (b.name === "App" && b.type === "svelte") return 1;

        if (a.type === b.type) return a.name < b.name ? -1 : 1;

        if (a.type === "svelte") return -1;
        if (b.type === "svelte") return 1;
      });
  }

  onMount(() => {
    fetch(`nested.json`).then(async (response) => {
      if (response.ok) {
        const data = await response.json();
        repl.set({
          components: process_example(data.files),
        });
        setTimeout(() => {
          console.debug(repl);
        }, 6000);
      }
    });
  });
</script>

<style>
  .foo {
    color: red;
  }
  /* div {
    color: red;
  } */
  .repl {
    height: 600px;
  }
</style>

<!-- <div class="foo">Hello from {name} component!</div> -->
<div class="repl">
  <Repl class="repl" workersUrl="workers" bind:this={repl} />

</div>
