<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();
  //funktio, joka poistaa rivin, kun painetaan poista- nappulaa.
  function poistaRivi() {
    dispatch('poistaRivi', { description, amount });
  }

  export let description;
  export let amount;
</script>

<div class="lista">
  <!-- lista, jossa on lisätyt kuvaukset ja luvut. Sen jälkeen on poista- nappula. -->
  <p>{description}</p>
  <p>{amount}€</p>
  <button on:click={poistaRivi}>Poista</button>
</div>

<style>
  .lista {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 10px;
  }

  button {
    padding: 5px;
    margin: 10px;
    background-color: #444;
    color: #ef9038;
    border: none;
    cursor: pointer;
  }

  button:hover {
    background-color: #555;
  }
</style>
