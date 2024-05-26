<script>
  //importatut komponentit ja transitiot
  import Kulut from './lib/Kulut.svelte';
  import { fly } from 'svelte/transition';
  import { onMount } from 'svelte';

  let naytaKuva = false;
  let tulot = [];
  let menot = [];
  let tuloLista = '';
  let tulotYhteensa = 0;
  let menoLista = '';
  let menotYhteensa = 0;

  onMount(() => {
    naytaKuva = true;
  });
  // funktio, joka lisää tulot- taulukkoon syötetyn arvon. Mukana myös validointi, että kenttään voi syöttää vain numeroita ja sen pitää olla enemmän kuin 0. Sivu ilmoittaa, jos syöte ei kelpaa.
  // tässä kohdassa kysyin tekoälyltä apua tuohon tulot = [] sisällön tekemiseen, kun en itse sitä millään saanut kirjoitettua niin, että se toimisi niinkuin haluaisin.
  //tulot =[] levittää sen olemassa olevan taulukon alkiot ja luo sinne uuden tulo- alkion, joka saa 2 ominaisuutta, kuvauksen ja summan. Joten taulukko päivittyy, kun siihen lisätään uusi olio.
  function lisaaTulo() {
    if (tulotYhteensa > 0 && !isNaN(tulotYhteensa)) {
      tulot = [
        ...tulot,
        { description: tuloLista, amount: parseFloat(tulotYhteensa) },
      ];
      tuloLista = '';
      tulotYhteensa = 0;
    } else {
      alert('Syötä kelvollinen summa');
    }
  }
  // funktio, joka lisää menot- taulukkoon syötetyn arvon. Mukana myös validointi, että kenttään voi syöttää vain numeroita ja sen pitää olla enemmän kuin 0. Sivu ilmoittaa, jos syöte ei kelpaa.
  // tässä kohdassa käytin sitä samaa koodinpätkää, mitä olin kysynyt tekoälyltä tuossa tulo- kohdassa. Eli nyt tuo menot = [] sisältö.
  //menot =[] levittää sen olemassa olevan taulukon alkiot ja luo sinne uuden meno- alkion, joka saa 2 ominaisuutta, kuvauksen ja summan. Joten taulukko päivittyy, kun siihen lisätään uusi olio.
  function lisaaMeno() {
    if (menotYhteensa > 0 && !isNaN(menotYhteensa)) {
      menot = [
        ...menot,
        { description: menoLista, amount: parseFloat(menotYhteensa) },
      ];
      menoLista = '';
      menotYhteensa = 0;
    } else {
      alert('Syötä kelvollinen summa');
    }
  }
  // funktio, jossa käydääm reduce- metodilla läpi jokainen alkio taulukosta ja laskee ne yhteen.
  function summa(luvut) {
    return luvut.reduce((yhteensa, luku) => yhteensa + luku.amount, 0);
  }
  // funktio, joka käy taulukon läpi ja poistaa valitun olion tulot- taulukosta.
  function PoistaTulo(event) {
    const { description, amount } = event.detail;
    tulot = tulot.filter(
      (luku) => luku.description !== description || luku.amount !== amount
    );
  }
  // funktio, joka käy taulukon läpi ja poistaa valitun olion menot- taulukosta.
  function PoistaMeno(event) {
    const { description, amount } = event.detail;
    menot = menot.filter(
      (luku) => luku.description !== description || luku.amount !== amount
    );
  }
</script>

<main>
  <h1>BUDJETTILASKURI</h1>
  <div id="laatikko">
    <h2>TULOT</h2>
    <!-- tulojen kuvaus ja lukema- kentät sekä lisää tulo- nappula -->
    <input type="text" bind:value={tuloLista} placeholder="Kuvaus" />
    <input type="number" bind:value={tulotYhteensa} placeholder="Summa" />
    <button on:click={lisaaTulo}>Lisää tulo</button>

    <ul>
      <!-- käydään läpi tulot- taulukon kaikki alkiot. Jokaiselle alkiolle luodaan komponentti, jossa se saa kaksi ominaisuutta. Lopussa tapahtumakuuntelija, joka poistaa tietorivin, kun painetaan poista- nappia.-->
      {#each tulot as tulo}
        <Kulut
          description={tulo.description}
          amount={tulo.amount}
          on:poistaRivi={PoistaTulo}
        />
      {/each}
    </ul>
    <!--kutsuu summa- funktiota, ja näyttää tulot- taulukon kaikkien lukujen yhteisarvon.  -->
    <h3>Yhteensä tulot: {summa(tulot)}€</h3>
  </div>
  <div id="laatikko">
    <h2>MENOT</h2>
    <!-- menojen kuvaus ja lukema- kentät sekä lisää meno- nappula -->
    <input type="text" bind:value={menoLista} placeholder="Kuvaus" />
    <input type="number" bind:value={menotYhteensa} placeholder="Summa" />
    <button on:click={lisaaMeno}>Lisää meno</button>

    <ul>
      <!-- käydään läpi menot- taulukon kaikki alkiot. Jokaiselle alkiolle luodaan komponentti, jossa se saa kaksi ominaisuutta. Lopussa tapahtumakuuntelija, joka poistaa tietorivin, kun painetaan poista- nappia.-->
      {#each menot as meno}
        <Kulut
          description={meno.description}
          amount={meno.amount}
          on:poistaRivi={PoistaMeno}
        />
      {/each}
    </ul>
    <!--kutsuu summa- funktiota, ja näyttää menot- taulukon kaikkien lukujen yhteisarvon. -->
    <h3>Yhteensä menot: {summa(menot)}€</h3>
  </div>
  <h2 id="loppusumma">
    <!--kutsuu funktioita, ja suorittaa vähennyslaskun, joka näyttää tulojen ja menojen erotuksen.  -->
    Budjettiä jäljellä: {summa(tulot) - summa(menot)} €
  </h2>
  <!--liikkuva kuva, jossa käytetty fly- transitionia. -->
  <div class="image-container">
    {#if naytaKuva}
      <img
        src="./pics/kolikot.png"
        alt="kolikot"
        height="200px"
        transition:fly={{ duration: 2000, x: 0, y: 300 }}
      />
    {/if}
  </div>
</main>

<style>
  h1 {
    margin-bottom: 20px;
  }

  h2 {
    margin: 20px;
  }

  h3 {
    margin-top: 10px;
  }

  input {
    margin: 5px;
    padding: 10px;
    background-color: #2c2c2c;
    color: #f0f0f0;
    border: 1px solid #444;
    border-radius: 5px;
  }

  button {
    padding: 10px;
    margin: 10px;
    background-color: #444;
    color: #ffffff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  button:hover {
    background-color: #555;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  #laatikko {
    display: inline-table;
    background-color: rgb(50, 50, 50);
    text-align: center;
  }

  #loppusumma {
    border-style: dotted;
    position: static;
    margin-left: 400px;
    margin-right: 400px;
    color: rgb(143, 197, 238);
    background-color: rgb(120, 54, 16);
    text-align: center;
  }
</style>
