<script>
  import { createEventDispatcher } from "svelte";
  import randomCoordinates from "random-coordinates";

  const dispatch = createEventDispatcher();

  function throwDart() {
    const coordinates = randomCoordinates().split(",").map(Number);
    dispatch("throw-dart", {
      coordinates,
    });
  }
</script>

<div>
  <button on:click={throwDart}>🎯</button>
</div>

<style>
  button {
    background: none;
    border: none;
    margin: 0;
    padding: 0;
    cursor: pointer;
    padding-left: 20px;
    padding-right: 20px;
    font-size: 120px;
  }
  button:active {
    background: none;
  }
</style>
