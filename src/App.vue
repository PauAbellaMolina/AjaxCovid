<template>
  <div id="app">
    <!-- Mentre s'estiguin fetcheand les dades de les APIs, mostra el spinner de loading -->
    <div v-if="!this.fetched">
      <div class="loading"><div class="elem-mid"><i class="fas fa-sync-alt fa-spin text-primary"></i></div></div>
    </div>
		<div class="container flex-shrink-0 my-5">
			<h1>Estadístiques del COVID19 per <span id="country_name"><span v-if="!this.mostrarCards">països</span><span v-else>{{ this.paisInput }}</span></span></h1>
			<h2 class="lead">Escriu el nom del país per veure la seva afectació</h2>
			
			<div class="row py-3">
				<div id="content" class="col-lg-12">
					<div class="form-inline">
						<div class="input-group">
              <!-- Barra de búsqueda. El que posem aqui serà el valor de la variable paisInput. Cada vegada que modifiquem el que estiguem buscant, crida al metode handleBusquedaPais. Quan fem el submit (que el podem fer clicent a l'icona o prement la tecla enter), crida al metode handlePaisInput -->
							<input class="search_query form-control" type="text" name="key" id="key" autocomplete="off" placeholder="Cercar..." v-model="paisInput" @input="handleBusquedaPais" @keyup.enter="handlePaisInput">
							<span class="input-group-btn">
								<button class="btn btn-info btn-flat disabled" v-on:click="handlePaisInput"><i class="fa fa-search"></i></button>
							</span>
						</div>
					</div>
          <!-- Si la variable paisInput NO esta buida, es a dir, l'usuari esta buscant algo a la barra de busqueda i la variable mostrarSugeriments es true, entra al if -->
          <div v-if="this.paisInput != '' && this.mostrarSugeriments">
            <div id="suggestions" class="card">
              <!-- Bucle for que per cada país dins de la variable paisosSugerits, renderitza un tag <p> amb el nom de cada país que coincideix amb la busqueda -->
              <div class="card-body" v-for="pais in this.paisosSugerits" :key="pais.id">
                <!-- Al clicar a algun país, crida al metode handleClickPaisSugerit -->
                <p v-on:click="handleClickPaisSugerit(pais)">{{ pais }}</p>
              </div>
            </div>
          </div>
				</div>
			</div>

      <!-- Si la variable mostrarCards es true i la variable notFound es false, es a dir, si s'ha trobat el país introduït i s'han trobat dades d'aquest, entra al if -->
      <div class="row" v-if="this.mostrarCards && !this.notFound">
        <!-- Afegim el component data (el blau) i li passem per props la ultima data de les dades trobades i filtrades pel país introduït -->
        <header-calendari-component :data="this.dadesFiltrades[0].dateRep"></header-calendari-component>
        <!-- Afegim el component infectats (el groc/taronja) i li passem per props el número d'infectats de les dades trobades i filtrades pel país introduït -->
        <header-infectats-component :infectats="this.dadesRecompteFiltrades[0].attributes.Confirmed"></header-infectats-component>
        <!-- Afegim el component morts (el vermell) i li passem per props el número de morts de les dades trobades i filtrades pel país introduït -->
        <header-morts-component :morts="this.dadesRecompteFiltrades[0].attributes.Deaths"></header-morts-component>
        <!-- Afegim el component recuperats (el verd) i li passem per props el número de recuperats de les dades trobades i filtrades pel país introduït -->
        <header-recuperats-component :recuperats="this.dadesRecompteFiltrades[0].attributes.Recovered"></header-recuperats-component>
      </div>
      <!-- En cas de que no haguem trobat dades o aquestes s'estiguin fetcheant encara, mostrem els components passant dades de "placeholder" -->
      <div class="row" v-else>
        <header-calendari-component :data="'N/A'"></header-calendari-component>
        <header-infectats-component :infectats="0"></header-infectats-component>
        <header-morts-component :morts="0"></header-morts-component>
        <header-recuperats-component :recuperats="0"></header-recuperats-component>
      </div>

      <!-- Si la variable mostrarCards es true i la variable notFound es false, es a dir, si s'ha trobat el país introduït i s'han trobat dades d'aquest, entra al if -->
      <div class="row mt-5" id="old_days" v-if="this.mostrarCards && !this.notFound">
        <!-- Bucle for que per cada objecte dins de la variable dadesFiltrades, renderitza el component main-dades-dia-component, que és la card amb l'informació diaria, passant per props el objecte sencer -->
        <div class="col-xl-2 col-lg-4 col-md-6" v-for="object in this.dadesFiltrades" :key="object.id">
          <main-dades-dia-component :dades="object"></main-dades-dia-component>
        </div>
      </div>
      <!-- Si la variable mostrarCards es true i la variable notFound TAMBÉ, és a dir, no s'ha trobat el país introduït, entra al if i mostra un missatge de país no trobat -->
      <div v-if="this.mostrarCards && this.notFound">
        <h1 class="w-100 mt-3 text-center">País no trobat</h1>
      </div>
		</div>
  </div>
</template>

<style>
  @import './css/main.css';
</style>


<script>
// Importem els components
import HeaderCalendariComponent from './components/HeaderCalendariComponent';
import HeaderInfectatsComponent from './components/HeaderInfectatsComponent';
import HeaderMortsComponent from './components/HeaderMortsComponent';
import HeaderRecuperatsComponent from './components/HeaderRecuperatsComponent';
import MainDadesDiaComponent from './components/MainDadesDiaComponent';

export default {
  name: 'App',
  components: {
    HeaderCalendariComponent,
    HeaderInfectatsComponent,
    HeaderMortsComponent,
    HeaderRecuperatsComponent,
    MainDadesDiaComponent
  },
  data() {
    return {
      paisInput: "", //El país que introdueix l'usuari a la barra de búsqueda
      dades: [], //Dades de les cards tal com les torna la api
      dadesFiltrades: [], //Dades de les cards NOMÉS del país de paisInput
      dadesRecompte: [], //Dades dels components de recompte (blau, groc/taronja, vermell, verd) tal com les torna la api
      dadesRecompteFiltrades: [], //Dades dels components de recompte (blau, groc/taronja, vermell, verd) NOMÉS del país de paisInput
      paisosSugerits: [], //Array dels paísos sugerits per la api de sugeriment de països
      fetched: false, //Boolean per saber quan s'han acabat de fetchear les dades de les apis
      mostrarCards: false, //Boolean per determinar si mostrar o no les cards d'informació diaria
      mostrarSugeriments: false,
      notFound: false //Boolean per saber quan NO shan trobat dades del país de paisInput o quan no s'ha trobat el país en sí
    }
  },
  //Abans de montar aquest component (App.vue), cridem al metode fetch
  beforeMount() {
    this.fetch();
  },
  methods: {
    //Metode que crida al metode obtenirDades i canvia a true i false les variables mostrarCards i mostrarSugeriments respectivament
    handlePaisInput() {
      this.obtenirDades();
      this.mostrarCards = true;
      this.mostrarSugeriments = false;
    },
    //Metode que crida al metode fetchPaisos i canvia a true la variable mostrarSugeriments
    handleBusquedaPais() {
      this.fetchPaisos();
      this.mostrarSugeriments = true;
    },
    //Metode que seteja la variable paisInput al string rebut per paràmetre, que és el país que l'usuari ha clicat de la llista de països sugerits
    handleClickPaisSugerit(pais) {
      this.paisInput = pais;
    },
    //Metode que filtra les dades de les variables dadesFiltrades i dadesRecompteFiltrades segons el país de la variable paisInput
    obtenirDades() {
      this.dadesFiltrades = this.dades.filter(item => item.countriesAndTerritories == this.paisInput);
      this.dadesRecompteFiltrades = this.dadesRecompte.filter(item => item.attributes.Country_Region == this.paisInput);
      //Si no troba dades, canvia a true la variable notFound
      if(this.dadesFiltrades[0] == undefined) {
        this.notFound = true;
        console.log("país no trobat");
      } else {
        this.notFound = false; 
      }
    },
    //Metode que fa el fetch a les apis que retornaran les dades de les cards amb informació diària i els components de recompte (blau, groc/taronja, vermell, verd)
    async fetch() {
      console.log("fetching")
      //Api cards amb informació diària
      const res = await fetch(`https://cors-anywhere.herokuapp.com/https://opendata.ecdc.europa.eu/covid19/casedistribution/json/`);
      let data = await res.json();
      data = data["records"];

      // Guardo a la variable dades les dades rebudes de la api en forma d'array
      this.dades = Object.keys(data).map((key) => {
        return data[key]
      })

      //Api components de recompte (blau, groc/taronja, vermell, verd)
      const resRecompte = await fetch(`https://cors-anywhere.herokuapp.com/https://services1.arcgis.com/0MSEUqKaxRlEPj5g/arcgis/rest/services/Coronavirus_2019_nCoV_Cases/FeatureServer/2/query?where=1%3D1&outFields=*&outSR=4326&f=json`);
      let dataRecompte = await resRecompte.json();
      dataRecompte = dataRecompte["features"];

      // Guardo a la variable dadesRecompte les dades rebudes de la api en forma d'array
      this.dadesRecompte = Object.keys(dataRecompte).map((key) => {
        return dataRecompte[key]
      })

      //Un cop acabats els fetch, canviem a true la variable fetched
      this.fetched = true;
      console.log("fetched");
    },
    //Metode que fa el fetch a la api de sugeriment de països, passant-li per la url en forma de query la variable paisInput
    async fetchPaisos() {
      const res = await fetch(`https://cors-anywhere.herokuapp.com/http://covid.codifi.cat/countries.php?country=`+this.paisInput);
      let data = await res.json();
      //Un cop rebudes les dades, les emmagatzemem dins la variable paisosSugerits
      this.paisosSugerits = data;
    }
  }
}
</script>
