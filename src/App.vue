<template>
  <div id="app">
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
							<input class="search_query form-control" type="text" name="key" id="key" autocomplete="off" placeholder="Cercar..." v-model="paisInput" @input="handleBusquedaPais" @keyup.enter="handlePaisInput">
							<span class="input-group-btn">
								<button class="btn btn-info btn-flat disabled" v-on:click="handlePaisInput"><i class="fa fa-search"></i></button>
							</span>
						</div>
					</div>
          <div v-if="this.paisInput != '' && this.mostrarSugeriments">
            <div id="suggestions" class="card">
              <div class="card-body" v-for="pais in this.paisosSugerits" :key="pais.id">
                <p v-on:click="handleClickPaisSugerit(pais)">{{ pais }}</p>
              </div>
            </div>
          </div>
				</div>
			</div>

      <div class="row" v-if="this.mostrarCards && !this.notFound">
        <header-calendari-component :data="this.dadesFiltrades[0].dateRep"></header-calendari-component>
        <header-infectats-component :infectats="this.dadesRecompteFiltrades[0].attributes.Confirmed"></header-infectats-component>
        <header-morts-component :morts="this.dadesRecompteFiltrades[0].attributes.Deaths"></header-morts-component>
        <header-recuperats-component :recuperats="this.dadesRecompteFiltrades[0].attributes.Recovered"></header-recuperats-component>
      </div>
      <div class="row" v-else>
        <header-calendari-component :data="'N/A'"></header-calendari-component>
        <header-infectats-component :infectats="0"></header-infectats-component>
        <header-morts-component :morts="0"></header-morts-component>
        <header-recuperats-component :recuperats="0"></header-recuperats-component>
      </div>

      <div class="row mt-5" id="old_days" v-if="this.mostrarCards && !this.notFound">
        <div class="col-xl-2 col-lg-4 col-md-6" v-for="object in this.dadesFiltrades" :key="object.id">
          <main-dades-dia-component :dades="object"></main-dades-dia-component>
        </div>
      </div>
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
      paisInput: "",
      dades: [],
      dadesFiltrades: [],
      dadesRecompte: [],
      dadesRecompteFiltrades: [],
      paisosSugerits: [],
      fetched: false,
      mostrarCards: false,
      notFound: false
    }
  },
  beforeMount() {
    this.fetch();
  },
  methods: {
    handlePaisInput() {
      this.obtenirDades();
      this.mostrarCards = true;
      this.mostrarSugeriments = false;
    },
    handleBusquedaPais() {
      this.fetchPaisos();
      this.mostrarSugeriments = true;
    },
    handleClickPaisSugerit(pais) {
      this.paisInput = pais;
    },
    obtenirDades() {
      //Filtro l'array de dades per país
      this.dadesFiltrades = this.dades.filter(item => item.countriesAndTerritories == this.paisInput);
      this.dadesRecompteFiltrades = this.dadesRecompte.filter(item => item.attributes.Country_Region == this.paisInput);
      if(this.dadesFiltrades[0] == undefined) {
        this.notFound = true;
        console.log("pais no trobat");
      } else {
        this.notFound = false; 
      }
    },
    async fetch() {
      console.log("fetching")
      const res = await fetch(`https://cors-anywhere.herokuapp.com/https://opendata.ecdc.europa.eu/covid19/casedistribution/json/`);
      let data = await res.json();
      data = data["records"];

      // Passo d'objecte a array les dades rebudes
      this.dades = Object.keys(data).map((key) => {
        return data[key]
      })

      const resRecompte = await fetch(`https://cors-anywhere.herokuapp.com/https://services1.arcgis.com/0MSEUqKaxRlEPj5g/arcgis/rest/services/Coronavirus_2019_nCoV_Cases/FeatureServer/2/query?where=1%3D1&outFields=*&outSR=4326&f=json`);
      let dataRecompte = await resRecompte.json();
      dataRecompte = dataRecompte["features"];

      // Passo d'objecte a array les dades rebudes
      this.dadesRecompte = Object.keys(dataRecompte).map((key) => {
        return dataRecompte[key]
      })

      this.fetched = true;
      console.log("fetched");
    },
    async fetchPaisos() {
      const res = await fetch(`https://cors-anywhere.herokuapp.com/http://covid.codifi.cat/countries.php?country=`+this.paisInput);
      let data = await res.json();
      this.paisosSugerits = data;
    }
  }
}
</script>
