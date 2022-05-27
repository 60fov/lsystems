<script lang="ts">
  import D0L from "./lib/D0L.svelte";

</script>

<main>
  <D0L />
</main>

<style lang="scss">
  :global(*) {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  :global(:root) {
    --border: 1px solid black;
    --padding: 10px;
  }

  main {
    background: whitesmoke;
    font-family: "Founders Grotesk Condensed", sans-serif;
  }

</style>
